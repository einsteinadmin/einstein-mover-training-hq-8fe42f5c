# Mover Training App — UX Workflow Audit
**Audited:** June 12, 2026  
**Auditor:** Albert (Cameron's COS)  
**Codebase:** `/tmp/Mike-Mover-Training` — React + Vite client, Express API, PostgreSQL

---

## Verdict

This is a solid first build with a clean visual design, well-structured data model, and good bones for a training system. The core curriculum is all wired up and the manager-signs-off / mover-demonstrates two-actor model is implemented correctly. The critical gap is **mobile ergonomics for the manager doing field marking** — the current UI favors end-of-day desktop entry over live in-the-field use, which is the primary real-world workflow. The second biggest issue is the **wrong default landing page for new trainees**: approved movers land on Einstein Games (a scoreboard iframe), not on their training. For a 22-year-old on day one, that's a confusing non-start. Three other pain points — no time-in-stage data for oversight, the onsite-eval iframe can't deep-link to a mover yet, and the pending approval experience has no polling — round out the top issues.

---

## Persona 1: New Mover, Day 1, Phone in a Parking Lot at 7am

### The flow, step by step

1. **Sign up** — Lands on Clerk's sign-up UI (standard email/password or OAuth). Clean, on-brand, mobile-friendly. No friction here.

2. **Onboarding page** — After first sign-in, the server sees status=pending, no requestedRole, and routes to `/onboarding`. The mover sees two dropdowns: Branch and Requested Role. Both are standard `<Select>` components. Workable on mobile, but "Requested role" defaults to "Mover" — most new hires won't touch that. Submitting sends the request.

3. **Pending wall** — After submit, they land on `/pending`. This is a dead-end holding screen with: the logo, a clock icon, "Awaiting approval," one sentence of explanation, their branch and requested role shown in a small table, and a sign-out link. **There is no auto-refresh.** The mover must close the app and reopen it (or manually reload) to find out if they've been approved. On a chaotic 7am crew start, this means the mover has no signal and no thing to do. No "check back" instruction, no estimated time. For a new hire who just downloaded the app and is standing in a parking lot, this is a complete stop.

4. **Approval** — A manager has to go into Settings, find the pending request, set the role and branch, and tap Approve. Only then does the pending page route to somewhere useful.

5. **Post-approval landing** — Once approved, `routeForUser()` sends trainees to `/games`. **This is the wrong default.** Einstein Games is an external scoreboard iframe showing competition rankings across the branch. A brand-new mover has never worked a shift. Their branch scoreboard is irrelevant to them. There is no onboarding message, no "welcome, here's what to do next," nothing. They have to discover the "Training" tab in the nav on their own.

6. **Portal** — Once they find `/portal`, it's good. Overall progress bar, a "Continue Learning" card pointing to the next item, and four stage cards with lock icons and progress bars. The Continue Learning card is the right UX — one button to pick up where they left off. The stage cards show lock/unlock state clearly.

7. **Stage page — Onboarding Field Training** — Here's where it gets confusing for sections 4-16. The mover sees items with a locked "Mover Demonstrated" button — it stays grayed out with a lock icon and the text "Locked until your manager marks this as Trained." For sections 1-3, the mover sees "Your manager will mark this complete during field training" with a lock icon. So the mover's experience during onboarding is mostly passive: they open the app, see a wall of lock icons, and wait. There's no clear instruction that this is normal and expected. A 22-year-old who doesn't know the system will wonder if the app is broken.

### Friction points

- **No auto-refresh on /pending.** The mover has to reload manually to discover approval. Source: `pending.tsx` — pure static render, no polling, no WebSocket.
- **Wrong post-approval destination.** `routeForUser()` in `App.tsx` line 165 sends all trainees to `/games`. Should be `/portal` for new trainees.
- **Onboarding lock state is unexplained.** In `stage.tsx`, sections 1-3 show a lock icon with "Your manager will mark this complete during field training." No guidance on what to do next, when the manager will show up, or how to move forward.
- **Nav tab label is "Training" not "My Training."** Minor, but the distinction matters when someone is confused about where to go.
- **No welcome / first-run experience.** Nothing says "here's how this works" after first login.

---

## Persona 2: Manager Training a New Hire in the Field

### Tap count from app-open to marking one item

Starting from a cold app launch (already logged in, session valid):

1. App loads → GET /me → routes to `/manage` (3-5 seconds on LTE, could be slower in a warehouse)
2. `/manage` loads — manager sees the branch dashboard with trainee list
3. Scroll to find the new hire's name in the list
4. Tap the trainee row → navigate to `/manage/trainee/:userId`
5. Page loads — header profile, then stage progression cards, then the onboarding checklist below the fold
6. See "Click to mark training items below" on the Onboarding stage card → tap → smooth-scroll to `#onboarding-checklist`
7. Scroll through sections to find the right item
8. Tap "Trained" button

**Total: approximately 6-8 taps and 2-3 scroll actions, with 2 page loads.** With gloves on and a phone in one hand, that's rough. On a slow connection in a warehouse, the two page loads could be 5-10 seconds each.

### Live-in-the-field vs end-of-day batch

**What the current UI favors: end-of-day batch entry.** The checklist is at the bottom of the trainee detail page, below a header card, below four stage cards with full progress bars. It's designed for a manager to sit down with the app, review the trainee's full record, scroll to the bottom, and methodically work through items. That's a desktop-at-a-desk workflow.

**What would beat a paper printout for live use:**
A dedicated "Mark Training" quick-action that goes directly to the checklist for a specific trainee — bypassing the profile header and stage overview entirely. Something like a manager home page showing "Active trainees being trained today" with one-tap access to their checklist, then a full-screen item list where each item is a large tap target with a single "Trained" button — no scrolling required for the button, no small UI. Ideally with offline queuing so marking works even when the warehouse has no signal.

**Current checklist design on mobile:** Items are cards with an icon, title, optional body text, and action buttons. The "Trained" button is a normal `<Button size="sm">` — small. On a phone with work gloves, small buttons are problematic. For sections 4-16, the manager's Trained button works, but "Mover Demonstrated" is permanently disabled (grayed out) since the mover marks that themselves — visually confusing because it looks like a broken button, not an intentional design.

### Key issues

- **No quick-access "active training" shortcut.** Manager must navigate: `/manage` → trainee list → trainee detail → scroll → checklist. No shortcut.
- **Scroll depth.** The `ManagerChecklist` component is rendered after all the stage cards in `trainee-detail.tsx`. On a 375px-wide phone, the checklist can be 600-800px below the fold. Source: `trainee-detail.tsx`, `ManagerChecklist` at the bottom of the JSX tree.
- **Small button targets.** `size="sm"` buttons in the checklist. On mobile with gloves, 32px button height is too small for reliable tapping.
- **No section jump / quick-nav.** The onboarding stage has 16 sections with 50+ items. There's no way to jump to Section 7 (Hog-Tie) without scrolling through Sections 1-6.
- **No "mark all in section" option.** If a manager walks a crew through an entire section, they tap Trained once per item. Section 9 (Moving Terms) alone has 9 items — 9 taps one at a time, waiting for each server round-trip before the next button enables.
- **Network round-trips are blocking.** Each item mark fires `updateProgress.mutate()` and `disabled={updateProgress.isPending}` while waiting. Source: `trainee-detail.tsx` line 346. You can't mark item 2 until item 1's API call returns.

---

## Persona 3: Mover Mid-Training

### Marking "Demonstrated" on their own items

For sections 4-16 of onboarding, the mover sees a "Mover Demonstrated" button that's locked until the manager marks Trained. Once unlocked, they tap it and it sends `{ demonstrated: !progress?.demonstrated }` to the API. This is clear enough, though the UI copy could be crisper — "Locked until your manager marks this as Trained" is fine but wordy.

The confirmed-demonstrated state reads: "Marked as demonstrated. Your manager will confirm the training to complete it." Accurate but passive. The mover might not understand that the item won't show as done until the manager confirms.

### Finding their next task

The "Continue Learning" card on the portal is the right design. It shows: the next item title (with `line-clamp-1` so long titles truncate), the stage and section names, and a "Resume" button. This is clean and correct. One tap gets them to the right stage page.

### Onsite eval material on a phone

The onsite eval stage (`stage.tsx`) renders items with photos. Photos use `grid grid-cols-2 gap-4` — two columns on mobile. At 375px wide, each photo is ~165px wide, which is small for a reference image but usable. The `line-clamp-1` truncation on item titles in the portal card could cut off important context.

The "Before We Begin" section has four reading items without point weights — these show a "Mark as Read" button (a single tap). The graded items show Pass/Fail buttons side by side. The Pass button is green, Fail is outlined with red text. The visual distinction is clear. Point deductions are shown on the Fail button: "Fail (−25)" — helpful.

### Sprint stages and sequential locking

Sprint stages use `computeItemLocks()` which sets `blocked = true` after the first incomplete item — meaning every subsequent item is locked. The lock icon is clear. The "Mark Complete" button fires on tap. The stage has no "Skip" button since `allowSkip: false` for sprints. This is logically sound.

For a 22-year-old new hire, the mental model is: "I can only do item 1. Once I tap Mark Complete, item 2 unlocks." That's learnable. What's less obvious is the MGMT sign-off items — items like "MGMT sign-off: Sprint completed on time and praise delivered" are listed as regular items that the mover can tap "Mark Complete" on themselves. There's no enforcement that the manager actually signed off. The app trusts self-marking here, which may or may not match the intended process. If managers are supposed to be the ones marking sign-off items, that's not enforced in the code.

**Lock/skip/flag model readability:**
- Lock icon (padlock) = clear
- Check circle = clear
- CircleDashed = "not started" — recognizable to anyone who's used a checklist app
- AlertTriangle on a skipped item reads "Skipped — needs manager review" — clear enough
- The "Flag & Skip" button label (visible in onboarding since `allowSkip: true`) could confuse someone who doesn't know what "flag" means in this context. "Skip for now (manager will review)" would be clearer

### Dead end: what if I'm stuck waiting for a manager?

A mover who has completed all the items they can do on their own has no signal that they're done for the day. The portal just shows a partial progress bar. There's no "you're all caught up for now — your manager needs to review X items" message.

---

## Persona 4: Branch Manager Weekly Oversight

### What they can see

On `/manage`:
- Active mover count
- Average completion percentage across all trainees
- Flagged skip count (prominent in red when non-zero)
- Trainee list with name, current stage, overall progress bar, skip badge
- Stage breakdown sidebar: per-stage trainee count in progress / done, average completion percent
- Recent Activity feed: who completed what item, in which stage

On `/manage/trainee/:userId`:
- Individual trainee header with branch and active status
- Per-stage progress with completed/total counts and skipped items count
- Flagged items sidebar (which items were skipped)
- Full onboarding checklist to mark

### What they CANNOT see

This is the meaningful gap list:

- **Days since last activity.** The API computes `lastActivityAt` for each trainee (source: `manager.ts` line 112) and it's in the `TraineeSummary` type, but the **manager UI never renders it.** `manager.tsx` shows name, stage, progress, and skips — no "last active" anywhere. A mover who hasn't opened the app in 2 weeks looks identical to one who logged in this morning.
- **Time-in-stage.** There's no `stageStartedAt` timestamp. You can't tell if someone has been in Onboarding Field Training for 2 days (normal) or 12 days (stalled).
- **Pace vs expected pace.** Each sprint has a minimum day count (Sprint 1 = 2 days, Sprint 6 = 5 days, etc. — defined in seed.ts). The app has no concept of whether a trainee is ahead of or behind the expected pace.
- **Who approved a trainee and when.** No approval timestamp visible.
- **Which specific sections are done vs not done** without drilling into the checklist. The stage card shows total items done, not a section-level breakdown.
- **Eval scores per trainee on the manager list.** The manager dashboard shows flagged skips but not the trainee's onsite eval score. You have to click into the individual trainee and then into the stage to find it.
- **A "behind pace" or "at risk" highlight.** No automated signal for who needs a check-in. The manager has to eyeball the list and notice who's at 0% after X days.
- **Training notes or free-text.** There's no place for a manager to leave notes on a trainee's record (e.g., "needs extra time on wrapping, check in next Thursday").

---

## Persona 5: Admin / Regional / Ops Leadership

### What exists

Admins get a branch filter dropdown on the manager dashboard — a `<Select>` that lets them pick "All Branches" or any individual branch. The trainee list, stage breakdown, and recent activity update based on this filter.

The Settings page (`/settings`) gives admins a full user table across all branches — name, role, branch, mover ID, status, and activate/deactivate.

### What's missing

- **No cross-branch comparison view.** Picking "All Branches" in the manager dashboard aggregates the stats (one avg completion %, one flagged skip count) but doesn't show per-branch breakdown side-by-side. You can't tell if Houston is at 40% avg completion and Dallas is at 80%.
- **No cohort view.** No way to see "all movers hired in May" or "all movers currently in Sprint 3."
- **No export.** No way to pull a CSV or report of trainee progress for a review meeting.
- **No admin-level analytics.** The Performance Log page has a leaderboard with branch filter, which is good, but training progress has no equivalent leadership view.
- **Branch filter is admin-only.** Managers are locked to their own branch (correct for permissions), but if a regional manager oversees multiple branches, there's no multi-branch role — they'd need admin access.

---

## Mobile Responsiveness Assessment

**Design posture: mobile-aware but not mobile-first.**

Evidence:
- Layout uses `md:` breakpoints for two-column layouts (e.g., portal stages are `grid-cols-1 md:grid-cols-2`, manager dashboard is `md:col-span-2`). Single-column on mobile, two-column on desktop — correct.
- The nav bar is a horizontal scroll strip (`overflow-x-auto`) — works on mobile but 5 nav items at varying widths may require scrolling. "Code of Geniuses" is a long label.
- Header shows user name only on `sm:` and above (`hidden sm:flex`). On mobile, you can't see who's logged in.
- The Settings user table (`table-layout`) is wrapped in `overflow-x-auto` — horizontal scroll on mobile. Five columns (name, role, branch, mover ID, status, actions) will require significant horizontal scrolling on a 375px screen. Not a field-use page so lower priority.
- No `viewport-fit=cover` or safe-area insets in the index.html, which could cause content to be clipped by notches on iPhone models with home indicator.
- Checklist item cards in `trainee-detail.tsx` use `flex-wrap` for button rows — buttons will stack on narrow screens, which is actually better than overflow.
- `useIsMobile()` hook is defined (768px breakpoint) but is **not used anywhere in the current app.** It exists in the hooks directory but zero pages import it. Mobile-specific logic would need to be built.

**iframe embeds on small screens:**
- Einstein Games: `h-[calc(100dvh-11rem)] min-h-[480px]` — renders as a 480px+ tall iframe. The external games app was designed for a "phone" view at `/branch/<slug>`, so the content should be mobile-optimized, but it's an external dependency. No control over its responsiveness from this codebase.
- Onsite Evals tracker: same iframe sizing. The external app's filter controls (dropdowns for status/mover/branch) are unknown from this code, but since the deep-link to a specific mover doesn't work yet (the app doesn't read URL params), movers see the full scoreboard with dropdowns they'd need to navigate themselves.

---

## Empty / Loading / Error States

**Loading:** Every page uses `<Skeleton>` components — clean shimmer placeholders that match the shape of the content. No blank white flash. Good.

**Empty states:** The manager trainee list has an empty state with a Users icon and "No movers found." The flagged items sidebar on trainee detail has a dashed card with "No flagged items." These are fine.

**Error states:** The server connectivity error shows "Can't reach the server / Try again" with a retry button — correct. The Code of Geniuses page has an AlertTriangle error state. Performance log has an `EmptyPanel` component for no data. Generally solid.

**Missing error state:** If a trainee navigates to `/stage/some-slug` and the stage doesn't exist, they get "Stage not found." — plain text, no actionable next step, no link back to portal. Small issue.

**No toast on mark-complete confirmation in manager checklist.** Actually it does fire a toast ("Marked as trained") — but there's no visible count update until the page re-fetches. The progress bar at the top of the trainee detail page doesn't update in real time after marking because it's in a parent component that would need its own query invalidation. Managers marking items won't see the progress bar move until they navigate away and back.

---

## Accessibility Basics

- All interactive elements use semantic HTML (`<button>`, `<a>`).
- Stage cards in the trainee detail that scroll to the checklist use `role="button"` and `tabIndex` with `onKeyDown` — keyboard accessible.
- Form labels are paired with inputs via `htmlFor` / `id` in onboarding and settings.
- Images use `alt` text (logo: "Einstein Moving Co.", item photos: `alt={item.title}`).
- Color is not the sole indicator of state — icons accompany color changes (green checkmark, orange lock, red triangle).
- Potential issue: "Mover Demonstrated" button in manager checklist is `disabled` and shows no tooltip explaining why. The `title="The mover marks this from their own training page"` is on the button element but `title` attributes don't work on `disabled` buttons in most browsers. Screen reader users and managers who don't know the system design won't know why it's disabled.

---

## Copy / Tone Assessment

Overall the copy is functional and clear. A few notes:

- "Lead In Training" (the Mover Training stage) uses inconsistent capitalization in `seed.ts` — the stage title is "Mover Training" but the slug is `lead-in`. The UI shows "Mover Training" which is correct.
- "Code of Geniuses" as a nav label is internal Einstein culture — fine for a company-facing app, but someone new might not know what it refers to. A short parenthetical "Code of Geniuses (Handbook)" would help on first encounter.
- "Flag & Skip" on the skip button in onboarding is accurate but "flag" is ops jargon. New hires may not parse it correctly. Consider "Skip — manager will follow up."
- "Signed off by [name] on [date]" on completed checklist items is exactly right — clear attribution.
- "MGMT sign-off" as an item title (in the sprint checklist) reads as internal notation, not as a trainee-facing instruction. Could be reworded as "Your manager confirms this sprint is done."
- The pending page copy is good and honest. "You will get access as soon as it is approved" sets the right expectation.

---

## Top 10 UX Fixes — Ranked by Impact

### 1. Fix the post-approval landing page for trainees
**Effort: Small**  
Change `routeForUser()` in `App.tsx` line 165 from `return "/games"` to `return "/portal"`. Trainees should land where their work is, not on a scoreboard they have no context for. If you want a welcome experience, a first-visit flag in localStorage could trigger a modal the first time they hit `/portal`.

### 2. Add polling or a "Check for approval" button on /pending
**Effort: Small**  
The pending page has no mechanism to detect approval without a manual reload. Add a polling call to `useGetMe` with a 15-30 second interval while on `/pending`, or add a prominent "Check status" button. This is the first impression for every new hire.

### 3. Add a "Last active" / time-in-stage column to the manager trainee list
**Effort: Small**  
`lastActivityAt` is already computed by the API and in the `TraineeSummary` type — it's just not rendered in `manager.tsx`. Add a "Last active X days ago" indicator to each trainee row. This is the single most useful signal for identifying stalled trainees without clicking into each one.

### 4. Add a manager "Quick Mark" shortcut from the trainee list
**Effort: Medium**  
Add a direct link from each trainee row on `/manage` to their onboarding checklist — bypassing the full trainee detail page. Something like a "Mark Training" secondary button that goes to `/manage/trainee/:userId#onboarding-checklist`. Even better: a dedicated lightweight modal that shows just the checklist for rapid in-field marking, without the full page load.

### 5. Make checklist buttons larger and add section quick-nav
**Effort: Medium**  
In `trainee-detail.tsx` ManagerChecklist, change `size="sm"` buttons to `size="default"` (44px height — meets Apple's minimum touch target guideline). Add a section jump menu at the top of the checklist so managers can navigate to "Section 7 — Hog-Tie" without scrolling through Sections 1-6. The seed data has 16 sections with codes 1-16 — these could be rendered as a sticky horizontal scroll strip of section buttons.

### 6. Add a "Waiting for manager" state message in trainee onboarding view
**Effort: Small**  
In `stage.tsx`, for trainees viewing the onboarding stage, add a banner at the top explaining the workflow: "Your manager will mark each skill as Trained during field training. For sections 4-16, tap 'Mover Demonstrated' once you've shown the skill." Currently the only explanation is per-item, buried under a lock icon.

### 7. Fix the "Mover Demonstrated" disabled button — explain it to managers
**Effort: Small**  
In `trainee-detail.tsx` ManagerChecklist, the disabled "Mover Demonstrated" button needs visible explanatory text, not just a `title` attribute (which doesn't work on disabled elements). Add a small `<p>` below it: "Mover marks this from their own app." Otherwise managers think it's a bug.

### 8. Add days-since-entry / pace indicator on the manager trainee detail page
**Effort: Medium**  
Show how long the trainee has been in their current stage. Requires storing `stageStartedAt` on first progress entry per stage (a schema addition) or approximating from the oldest `completedAt` in a stage. Compare against the minimum day counts defined in the curriculum (sprint minimums are in seed.ts descriptions). Surface as "Day 4 of minimum 5 for Sprint 1" or a simple "On track / Behind pace" badge.

### 9. Add a cross-branch summary table for admins
**Effort: Medium**  
On the admin view of `/manage` with "All Branches" selected, the current aggregate stats lose all branch context. Add a per-branch row breakdown: branch name, trainee count, avg completion, flagged skips, last activity date. This gives regional leadership a true at-a-glance status without clicking into each branch filter.

### 10. Fix the onsite-eval iframe deep-link experience
**Effort: Large** (requires changes to the external onsite-eval-tracker app)  
Per the memory file, the onsite-eval tracker doesn't read URL parameters, so trainees can't be automatically filtered to their own data. When that app gains URL param support, wire up the `moverId` field that's already in the user model. In the meantime, add a brief instruction above the iframe for trainees: "Find your name in the dropdown to see your eval history." The current experience shows a full multi-mover scoreboard to trainees with no guidance.

---

## Summary Table

| Fix | File(s) | Effort |
|-----|---------|--------|
| Fix trainee landing page | `App.tsx:165` | Small |
| Poll or button on /pending | `pending.tsx` | Small |
| Show lastActivityAt in manager list | `manager.tsx` | Small |
| Manager Quick Mark shortcut | `manager.tsx`, `trainee-detail.tsx` | Medium |
| Larger checklist buttons + section nav | `trainee-detail.tsx` | Medium |
| Explain onboarding lock state to trainee | `stage.tsx` | Small |
| Fix disabled Mover Demonstrated label | `trainee-detail.tsx` | Small |
| Pace indicator on trainee detail | Schema + `trainee-detail.tsx` | Medium |
| Cross-branch summary for admins | `manager.tsx` | Medium |
| Onsite eval deep-link | External app + `onsite-evals.tsx` | Large |
