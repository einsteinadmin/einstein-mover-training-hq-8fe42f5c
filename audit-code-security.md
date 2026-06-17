# Code & Security Audit — Mover Training App
**Grade: B**
**Audit date:** 2026-06-12

## Verdict

This is a competently built, domain-appropriate app. The core auth model is sound: every route carries `requireAuth → loadLocalUser → requireApproved` (or `requireStaff`) in the right order, IDOR risks on the trainee progress write path are properly blocked, and the manager router's per-route guard pattern exists for a documented reason (the router-level guard leak). Completion and scoring are server-authoritative. The weakest areas are (1) a wildcard CORS policy that trusts any origin in production, (2) the seed script wiping all trainee progress with no guard, (3) missing DB indexes on the hot `progress.userId` column, (4) the `UNLOCK_ALL_STAGES` env flag bypassing all gating with no scope limit, and (5) a manager branch-scope information leak in the recent-activity query. No SQL injection, no credential leakage in logs, no broken auth chains were found. The overall posture is solid for an internal tool at this scale, but the five items below should be addressed before the app grows its user base.

---

## Critical

### C1 — Wildcard CORS allows any origin to make credentialed API requests
**File:** `artifacts/api-server/src/app.ts:38`

```ts
app.use(cors({ credentials: true, origin: true }));
```

`origin: true` is an alias for "reflect the caller's Origin header verbatim." Combined with `credentials: true`, this tells the browser to accept cross-origin credentialed requests from **any** site. An attacker who tricks a logged-in manager into visiting a malicious page can make authenticated API calls (approve users, read trainee progress across the branch, set `active: false` on any user) on their behalf. The Clerk Bearer-token bridge in the API client means the real attack surface depends on whether cookies or tokens are used, but the reflected-origin policy is still wrong for a production app.

**Fix:** Restrict `origin` to an explicit allowlist:
```ts
const ALLOWED_ORIGINS = new Set([
  process.env.CLIENT_ORIGIN ?? "https://your-training-app.replit.app",
]);
app.use(cors({
  credentials: true,
  origin: (origin, cb) =>
    !origin || ALLOWED_ORIGINS.has(origin)
      ? cb(null, true)
      : cb(new Error("Not allowed by CORS")),
}));
```

---

## High

### H1 — `seed.ts` unconditionally deletes all trainee progress
**File:** `lib/db/src/seed.ts:547-549`

```ts
await db.delete(progressTable);
await db.delete(itemsTable);
await db.delete(sectionsTable);
await db.delete(stagesTable);
```

Re-seeding the curriculum deletes every trainee's progress record with no guard, no prompt, no backup. There is no idempotency check — if someone runs `pnpm --filter @workspace/db run seed` on the production database after curriculum content changes (or by mistake), all completion records are permanently gone. This is a data-loss footgun that will hurt when it fires.

**Fix:** Add a `--wipe` CLI flag that must be explicitly passed to delete progress. Default behavior should be curriculum-upsert only (insert-on-conflict-do-update for stages/sections/items keyed on their slug/code, leaving progress rows untouched). Track item identity by stable code/slug so re-seeding with changed content doesn't orphan existing progress.

---

### H2 — Manager recent-activity query is not branch-scoped
**File:** `artifacts/api-server/src/routes/manager.ts:626-641`

```ts
const recentRows = trainees.length
  ? await db
      .select()
      .from(progressTable)
      .orderBy(desc(progressTable.updatedAt))
      .limit(200)    // ← no userId or branch filter
  : [];
const recentActivity = recentRows
  .filter((r) => traineeNameById.has(r.userId))  // post-filter in JS
  .slice(0, 12)
  ...
```

The query fetches the 200 most recently updated progress rows across **all branches and all users** and then filters them in JavaScript against the set of trainees that belong to the requesting manager's branch. This is fine for correctness (the JS filter ensures only in-branch trainees appear), but it is wasteful at scale and creates an accidental information channel: if a cross-branch trainee happens to appear in the top-200 but is filtered out, their `userId` and `itemId` are read off the DB connection anyway. More concretely, if the app grows to thousands of trainees, the in-memory filter will silently under-report because the top-200 global rows may not include any from the manager's branch.

**Fix:** Add `inArray(progressTable.userId, trainees.map(t => t.id))` to the query instead of fetching globally and post-filtering:
```ts
const traineeIds = trainees.map((t) => t.id);
const recentRows = traineeIds.length
  ? await db
      .select()
      .from(progressTable)
      .where(inArray(progressTable.userId, traineeIds))
      .orderBy(desc(progressTable.updatedAt))
      .limit(12)
  : [];
```

---

### H3 — No database index on `progress.userId`
**File:** `lib/db/src/schema/progress.ts`

The `progress` table has a composite `unique(userId, itemId)` constraint (which Postgres uses to enforce uniqueness) but Postgres does **not** automatically create a B-tree index on `userId` alone from a multi-column unique constraint. Every call to `getProgressMap(userId)` — which runs on every request that touches training data — does a sequential scan of the full `progress` table once there are more than a handful of rows. `loadAllStages()` is also called on virtually every route without caching, adding 3 sequential-scan DB round-trips per request.

**Fix:** Add explicit indexes:
```ts
// in lib/db/src/schema/progress.ts
(t) => [
  unique().on(t.userId, t.itemId),
  index("progress_user_id_idx").on(t.userId),
]
```
Also add `index("progress_item_id_idx").on(t.itemId)` to support the manager summary's item-level aggregation. Consider a short-lived in-process LRU cache for `loadAllStages()` since the curriculum is static between seeds.

---

### H4 — `UNLOCK_ALL_STAGES` bypasses all gating with no scope limitation
**File:** `artifacts/api-server/src/lib/training.ts:192-194`, `artifacts/api-server/src/index.ts:18-24`

```ts
export function unlockAllStagesEnabled(): boolean {
  return process.env.UNLOCK_ALL_STAGES === "true";
}
```

When this flag is set, `computeStageStates` marks every stage unlocked and `computeItemLocks` returns an empty set for **every user** — trainees, managers, and admins alike — across the entire application. The startup warning (`index.ts:23`) only fires when `NODE_ENV=production` AND the flag is set, meaning it is silently skipped in development where Replit environments often run. There is no per-user or per-session scope; it is global and binary.

**Fix:** (a) Emit the startup warning regardless of `NODE_ENV`. (b) Consider scoping the flag to a list of allowed `clerkUserId`s stored in the env var (`UNLOCK_ALL_STAGES_FOR=clerk_abc123,clerk_def456`) so only specific accounts are unlocked. (c) Add a high-visibility banner to the API health endpoint when the flag is active so ops can detect it in monitoring.

---

## Medium

### M1 — `resolveLocalUser` first-user bootstrap has a race condition
**File:** `artifacts/api-server/src/lib/auth.ts:79-106`

```ts
const count = await db.select({ id: usersTable.id }).from(usersTable);
const isFirstUser = count.length === 0;
```

Two simultaneous sign-ins on a cold database (before any user exists) can both read `count.length === 0`, both attempt to insert an admin record, and one will win the unique constraint on `clerkUserId` while the other crashes with a 500. In practice this is a cold-start scenario that only happens once, but the crash is user-visible and leaves the losing request unanswered. The same check-then-insert pattern on `email` lookup (line 66-76) is also technically racy, though the unique constraint on `email` prevents data corruption.

**Fix:** Wrap the count → insert in a Postgres advisory lock or use `INSERT ... ON CONFLICT DO NOTHING RETURNING *` with a fallback re-select. At minimum, catch the unique-constraint violation at the insert step and retry with a re-select rather than letting a 500 propagate.

---

### M2 — `result` and `score` schema types are unconstrained strings/integers
**File:** `lib/db/src/schema/progress.ts:31-32`, `lib/api-zod/src/generated/api.ts`

```ts
result: text("result"),      // no check constraint — any string persists
score: integer("score"),     // no range constraint
```

The Zod layer on the write path only accepts `"pass" | "fail" | null` for `result` and numeric `score`, which is correct. But nothing prevents a future code path (admin tooling, a migration, a direct DB query) from writing arbitrary strings into `result`. In Postgres, a check constraint is the only reliable way to enforce domain values at the storage layer.

**Fix:** Add a check constraint in the Drizzle schema:
```ts
result: text("result")
  .$type<"pass" | "fail">()
  // add a Postgres check via a custom SQL migration or drizzle-kit check()
```
And clamp `score` to `[0, 100]` with a check constraint since onsite eval scores are always 0-100.

---

### M3 — `notes` field has no length limit
**File:** `lib/api-zod/src/generated/api.ts:156`, `artifacts/api-server/src/routes/progress.ts:103-113`

```ts
"notes": zod.string().nullish()   // no max length
```

A manager or trainee can submit a `notes` field of arbitrary length (megabytes of text) on any progress write. This is stored in a Postgres `text` column with no limit. While Postgres handles large text gracefully, extremely large payloads can cause memory pressure in the Node process and slow down request processing.

**Fix:** Add `.max(2000)` (or a similarly reasonable limit) to the `notes` Zod field in the OpenAPI spec and regenerate. Also add `express.json({ limit: '50kb' })` to `app.ts` to cap the overall request body size.

---

### M4 — Branch values are free-form strings — no canonical validation on write paths
**Files:** `artifacts/api-server/src/routes/manager.ts:382-383`, `artifacts/api-server/src/routes/me.ts:48`

The `branch` field on user creation, onboarding, and approval accepts any non-empty string. A manager could accidentally type "north austin" instead of "North Austin" and create a visibility silo — they would see their own user but that user would not appear in the `normalizeBranch()`-normalized performance leaderboard lookups. There is also no validation that the submitted branch name is one of the known Einstein branches.

**Fix:** Maintain a canonical `ALLOWED_BRANCHES` list server-side (already present as `BRANCH_CANONICAL` keys in `lib/sheet.ts`) and validate the branch field against it on all write paths, returning a 400 with the list of valid options.

---

### M5 — Iframe embeds have no `sandbox` attribute and allow `clipboard-read/write`
**Files:** `artifacts/training/src/pages/einstein-games.tsx:21-27`, `artifacts/training/src/pages/onsite-evals.tsx:19-27`

```tsx
<iframe
  src={src}
  allow="fullscreen; clipboard-read; clipboard-write"
  ...
```

The iframes embed external Replit apps with clipboard access granted and no `sandbox` attribute. Without `sandbox`, the embedded app can execute scripts, submit forms, navigate the top frame, and open popups. The `clipboard-write` permission grants the embedded content the ability to overwrite whatever is on the logged-in user's clipboard — an unusual capability to grant to a third-party embed.

**Fix:** Add `sandbox="allow-scripts allow-same-origin allow-forms"` (adjust per what the embedded apps actually need) and drop `clipboard-read; clipboard-write` from the `allow` attribute unless the Games or eval app explicitly requires it. If the scoreboard only displays data, `allow-scripts allow-same-origin` is sufficient.

---

### M6 — In-memory cache for sheet/doc data has no size cap and holds stale data indefinitely on parse errors
**Files:** `artifacts/api-server/src/lib/sheet.ts:548-621`, `artifacts/api-server/src/lib/codeOfGeniuses.ts:238-267`

Both fetchers cache a single parsed object in module-level singletons with a 5-minute TTL. The `SheetData` object contains parsed arrays of mover rows, fulfillments, damages, reviews, attendance, and driver scores — potentially thousands of rows on a busy season. The stale-on-error behavior is correct but means that on a long-running process, cached data could be days old if the Google export endpoint becomes permanently unavailable. There is also no mechanism to evict the cache on graceful shutdown, so memory usage grows with season data size and never shrinks.

**Fix:** This is acceptable for a single-server app at current scale. Add a `fetchedAt` freshness check that emits a warning metric when stale data is >30 min old. Document the restart-to-refresh workaround. If the app is ever scaled to multiple processes, move caching to Redis.

---

## Low

### L1 — `result` column uses `text` type, not an enum — schema/code divergence
**File:** `lib/db/src/schema/progress.ts:31`

Drizzle's `.$type<"pass" | "fail">()` call only affects TypeScript types, not the actual Postgres column type. The actual DB column is plain `text`. If a direct-SQL update writes `"FAIL"` or `"Pass"`, the TypeScript layer will not catch it, and `result === "fail"` comparisons in `computeGradedScore` will silently treat the row as neither pass nor fail (no deduction).

**Fix:** Use a Postgres enum or add a check constraint. At minimum, normalize `result` to lowercase on the write path with `.toLowerCase()` before persisting.

---

### L2 — `loadAllStages()` is called 3-5 times per request with no caching
**File:** `artifacts/api-server/src/lib/training.ts:51-74`

`loadAllStages()` issues 3 sequential DB queries (stages, sections, items). On the trainee progress PUT route alone it is called twice: once to find the stage type, once inside `computeStageStates` (indirectly via the caller). On the manager summary route it is called in a `Promise.all` inside a per-trainee loop. The curriculum is seeded once and changes only on explicit re-seed, making it an ideal candidate for a short-lived in-process cache (e.g. 60-second TTL, bust on seed).

**Fix:** Wrap `loadAllStages()` in a simple module-level cache with a TTL and a `bustStagesCache()` export that the seed script can call (or the ops team can trigger via a health endpoint).

---

### L3 — `computeItemLocks` is O(items²) for ordered stages
**File:** `artifacts/api-server/src/lib/training.ts:237-258`

The sequential item lock logic iterates all items, and for each item checks `itemIsDone(progress.get(item.id))` which is O(1) via the Map. The outer loop is O(n) for n items per stage. This is fine for the current curriculum (~100 items across 4 stages). If lead/mover training grows significantly, this remains acceptable, but it is called redundantly (see L2 above).

No fix required at current scale; note for future growth.

---

### L4 — `cookie-parser` is installed but never used
**File:** `artifacts/api-server/package.json`, `artifacts/api-server/src/app.ts`

`cookie-parser` appears in `package.json` dependencies but is not registered in `app.ts`. The auth flow uses Clerk Bearer tokens (not cookies) for API calls. Dead dependency.

**Fix:** Remove `cookie-parser` and `@types/cookie-parser` from `package.json`.

---

### L5 — No rate limiting on authentication-adjacent endpoints
**Files:** `artifacts/api-server/src/routes/me.ts:32` (`POST /onboarding`), `artifacts/api-server/src/routes/manager.ts:360` (`POST /users`), `artifacts/api-server/src/routes/manager.ts:437` (`POST /users/:id/approve`)

There is no rate limiting on any API endpoint. For an internal app protected by Clerk auth this is low risk (an attacker needs a valid Clerk token), but repeated automated calls to `/users` (add user) or `/users/:id/approve` by a compromised manager account could spam the system. Express-rate-limit is a one-line addition.

**Fix:** Add `express-rate-limit` with a permissive limit (e.g., 100 req/min per IP) as a global middleware to prevent runaway clients.

---

## Top 5 Fixes in Priority Order

1. **[C1] Fix wildcard CORS.** Replace `origin: true` with an explicit allowlist. This is the only finding that could enable cross-site request forgery from any attacker who can get a logged-in user to visit a link. One-line fix with a 15-minute implementation.

2. **[H1] Guard `seed.ts` from destroying live progress data.** Add a `--wipe` flag requirement before deleting progress. Convert stage/section/item inserts to upserts. This prevents an irreversible production data-loss incident.

3. **[H2 + H3] Fix the manager recent-activity query and add the `progress.userId` index.** The index is a migration; the query fix is a 5-line change. Together they prevent a correctness bug (under-reporting for larger branches) and a performance cliff as the app grows.

4. **[H4] Scope or document `UNLOCK_ALL_STAGES` more defensively.** Emit the warning in all environments, not just production. This is a one-line change that prevents a silent auth bypass going unnoticed in staging.

5. **[M5] Add `sandbox` to the iframe embeds and drop unnecessary clipboard permissions.** The Einstein Games and Onsite Eval iframes embed third-party Replit apps with no sandboxing. Adding `sandbox="allow-scripts allow-same-origin allow-forms"` takes 5 minutes and meaningfully reduces the blast radius if either embedded app is ever compromised.
