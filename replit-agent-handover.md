# Mover Training App — Implementation Handover
**Date:** 6/12/2026 · **From:** Albert (Cameron's COS) · **For:** Mike, to feed to the Replit agent
**Sources:** full code+security audit and UX/workflow audit in this folder (file:line references there).

## How to use this doc

Each work item below is written to be pasted into the Replit agent as a task, individually or in batches. They're ordered: **P0 = before any pilot**, **P1 = first week of pilot**, **P2 = the next module**. Suggested batch: give the agent all of P0 in one session — they're small and independent.

Tip for the agent session: this repo keeps design decisions in `.agents/memory/` — tell the agent to read `replit.md` and that folder first, and to record any new non-obvious decision the same way. It's been doing this well; keep it up.

---

## P0 — before the pilot (one short agent session)

**P0.1 — Lock CORS to known origins.**
In `artifacts/api-server/src/app.ts` (~line 38), CORS is `origin: true` (reflects any caller). With cookie auth, any website a logged-in user visits can call our API as them. Replace with an explicit allowlist (the production app origin + localhost dev origins). One line plus a small env-driven list.

**P0.2 — Make the seed script safe to re-run.**
`lib/db/src/seed.ts` (~lines 547-549) deletes ALL progress rows before reseeding. The first time the curriculum gets edited after go-live, every trainee's training record is destroyed. Require an explicit `--wipe` flag for destructive mode; default mode should upsert stages/sections/items by slug/code and never touch the progress table. This is the most important item in this doc.

**P0.3 — Enforce manager-only sprint sign-off items on the server.**
Sprint items titled "MGMT sign-off: …" are currently regular sprint items — a trainee can mark them complete themselves. The server only enforces manager-only marking for the checklist stage. Add a `managerOnly` boolean to the items schema (set it in seed for every "MGMT sign-off" item, the Lead prerequisites, performance review, and driver-test items), and have `PUT /items/:itemId/progress` return 403 when a trainee writes a manager-only item. Mirror the UI treatment the onboarding checklist already has ("your manager marks this"). Without this, stage gates are self-certifiable and the two-actor integrity story only covers Stage 1.

**P0.4 — Trainees land on training, not the scoreboard.**
`routeForUser()` in `artifacts/training/src/App.tsx` (~line 165) sends trainees to `/games`. A day-1 new hire should land on `/portal` ("Continue Learning"). One-line change.

**P0.5 — The pending screen should notice approval.**
`/pending` never re-checks status; a new hire in the parking lot stays stuck until they manually reload. Poll `/me` every ~20s while on that page (or add a "Check status" button). First impression for every hire.

**P0.6 — Show "Last active" on the manager roster.**
The API already returns `lastActivityAt` per trainee; `manager.tsx` just doesn't render it. Add "Last active X days ago" to each row — it's the single best stalled-trainee signal and it's already computed.

**P0.7 — Small hardening batch.**
(a) Add `sandbox="allow-scripts allow-same-origin allow-forms"` to the Einstein Games and onsite-eval iframes and drop `clipboard-read; clipboard-write`. (b) Add an index on `progress.userId` (every request scans this table without it). (c) Fix the manager recent-activity query that fetches 200 rows globally then filters in JS — scope it to the manager's branch in SQL. (d) Log the `UNLOCK_ALL_STAGES` warning in all environments, not just production.

## P1 — first week of pilot (field-speed for managers)

**P1.1 — Quick Mark path.** From the `/manage` roster, a one-tap shortcut straight into a trainee's onboarding checklist (skip the full detail page) — ideally a lightweight modal for rapid marking. Today marking one item is 6-8 taps and two page loads; that loses to a paper printout.

**P1.2 — Field-sized checklist controls.** In `trainee-detail.tsx`: bump the sm buttons to default (44px touch target) and add a sticky section quick-nav strip (the 16 onboarding sections) so a manager can jump to "Hog-Tie" without scrolling.

**P1.3 — Explain the two-actor flow in both directions.** Trainee side (`stage.tsx`): a banner on the onboarding stage — "Your manager marks items as Trained; for sections 4-16, you tap Mover Demonstrated after you've shown the skill." Manager side: the disabled "Mover Demonstrated" button needs visible text ("mover marks this from their own app") — the current `title` tooltip doesn't show on disabled elements and reads as a bug.

**P1.4 — Onsite-eval iframe guidance.** Until the eval-tracker app supports URL params (tracked in `.agents/memory/onsite-eval-embed.md`), add one instruction line above the iframe: "Find your name in the dropdown to see your eval history."

## P2 — the next module: pace tracking (Cameron's #1 roadmap pick)

Design first, then build — this is the bridge to Anne's 60-day performance system, so loop Cameron/Anne on the design before the agent builds it.

- Record `stageStartedAt` per user per stage (first progress write, or stage unlock).
- Sprint minimum days currently live as prose in seed descriptions — promote to a structured `minDays` field on sections.
- Trainee detail + roster: "Day 4 of min 5 — Sprint 1" and an On pace / Behind badge.
- Admin view: per-branch summary table (branch, trainee count, avg completion, flagged skips, oldest inactivity) — today "All branches" loses all branch context.
- Future hooks (design for, don't build yet): Day-7 / Day-12 markers per trainee feeding Anne's check-in cadence and the affirmative-keep decision.

## Open questions for Mike (not agent tasks)

1. **Field workflow:** do managers mark items live in the field or batch at end of day? (Shapes how far to push P1.1's modal.) Cameron explicitly wants your read.
2. **Videos:** every item has a `videoUrl` slot already rendered by the UI. Plan for Austin L's library + hosting (unlisted YouTube vs Drive)?
3. **Identity:** EMMA IDs are hand-typed per user. Fine for pilot — but agree with Matisen on a sync source (EMMA/Rippling) before the app needs per-mover data (Samsara, bonus).
4. **Eval-tracker wiring:** you own both apps — add URL-param support to the eval tracker so this app can deep-link a mover, and consider feeding the 3-passing-evals Lead prerequisite from real data instead of manual logging.
5. **Sprint convergence:** your 8 mover sprints vs Anne's 9 handbook sprints in the 60-day SOP — one list before both ship.
6. **Pilot branch + curriculum sign-off:** Cameron's go-live bar is you + Brian confirming the 205 seeded items match current field practice.
