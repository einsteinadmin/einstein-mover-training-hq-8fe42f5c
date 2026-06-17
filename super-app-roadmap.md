# Mover Super App — Roadmap
**Date:** 6/12/2026 · **For:** Cameron → Mike (Matisen consulted) · **Status:** Cameron-review draft

## The decision this doc records

**Mike's training app (`einsteinadmin/Mike-Mover-Training`) is the Mover Super App.** Not a module of it — the chassis it grows into. Decided by Cameron 6/12/2026 after the code review.

Why this call is safe: the app already has the hard parts every module needs — login, roles (mover/manager/admin), branch scoping, a real database, and an API contract. The 2025 super-app prototype (`docs/mover-super-app/index.html`) imagined ~8 modules; Mike has independently built the three hardest ones. Everything else is "add a tab," not "start over."

**Ownership:** Mike drives, building with the Replit agent. Matisen consults — pulled in formally when identity sync (EMMA/Rippling) starts, because that's where his world begins.

## Scoreboard: prototype vision vs. what Mike built

| Prototype module | Status in Mike's app | Notes |
|---|---|---|
| Training Guide (staged skills, sign-offs, manager gates) | ✅ **Built, deeper than the mockup** | 4 stages, 205 items, two-actor sign-off, server-enforced. Mockup wanted PIN/QR sign-off; Mike's manager-login flow is arguably better. |
| Handbook / Code of Geniuses | ✅ **Built** | Live searchable wiki from the Google Doc. Mockup only had it as quiz + chatbot source. |
| Einstein Games | ✅ **Embedded** | Iframe, deep-links movers to their branch. |
| Performance stats (reviews, damages, attendance) | 🟡 **Half-built** | Performance Log reads the Games sheet. Mockup's version adds rings, trends, coaching tips. |
| Leaderboard ("The Board") | 🟡 **Half-built** | A leaderboard endpoint exists in the API; mockup adds categories + branch ranks. |
| SMART Driving / Samsara | ❌ Not built | Driver gate exists in Lead Training but is manually logged. |
| Bonus Simulator ("Money Moves") | ❌ Not built | Mockup's killer feature: sliders showing how safety/hours/damages/reviews move your bonus. |
| Attendance Planner (point gauge, "what if I call out") | ❌ Not built | Pairs with callout log. Needs Rippling/attendance data. |
| Career Path (Copilot → Lead General trail, pay bands) | ❌ Not built | But Lead Training prerequisites are already career gates in checklist form — closer than it looks. |
| Badges + streaks + milestones | ❌ Not built | Pure motivation layer; rides on data from everything above. |
| Daily Quiz | ❌ Not built | Cheap to add; content from Code of Geniuses. |
| My Schedule (today's moves, crew) | ❌ Not built | Needs EMMA dispatch data — biggest integration lift, also overlaps the iPad field app's territory. Park it. |
| Ask Einstein (AI chat over policies) | ❌ Not built | Real version = AI over the Code of Geniuses doc. Later. |
| The Feed (reviews/praise/milestones) | ❌ Not built | Praise data lives in Monday; nice culture layer, not urgent. |

## Build order (Cameron's ranking, 6/12)

1. **Pace tracking + check-ins** — days-in-stage vs. sprint minimums, "on pace / behind" flags on the manager dashboard, Day-7/12 hooks. This is the manager-oversight promise the landing page makes, and it's the bridge to Anne's 60-day performance system. Mostly built from data the app already has (timestamps exist on every sign-off).
2. **Samsara driving scores** — replaces a gate that's already in the app as manual entry (driver test, 10 hours, 95+ score). Clear scope, obvious win.
3. **Bonus calculator** — the biggest mover-WIFM in the whole prototype, but the heaviest data dependency (payroll-grade numbers, comp formula). Do it third so it lands on a proven app, not a pilot.
4. **Career path + badges** — the retention/aspiration layer. Cheap to render once 1–3 supply the data; meaningless before then.

Parked deliberately: My Schedule (EMMA dispatch — Matisen territory, overlaps iPad app), Ask Einstein (AI layer, revisit when content is loaded), The Feed.

## Standing constraints

- **Identity is the tripwire.** Today: Clerk login + hand-typed EMMA ID per user. Fine for a pilot; breaks at 400 movers and blocks every data-driven module above (Samsara, bonus, attendance all need to know *which mover this is*). Before module 2 ships, Mike + Matisen need 30 minutes on "where does the mover roster come from" — likely answer: sync from EMMA or Rippling, keep Clerk for login only.
- **Coexists with the iPad field app.** The field app (einstein-mobile) runs the *job* on move day; this app develops the *person*. Different devices, different moments. Don't merge; eventually share identity.
- **Three Replit apps, one sheet.** Training app + Einstein Games + onsite-eval tracker are stitched by iframes and the S3 Google Sheet. Acceptable now; before app #4 or module 3, have the data-flow conversation.
- **One sprint list, not two.** Mike's 8 mover-training sprints and the 9 handbook sprints in Anne's 60-day Phase 2 SOP are two drafts of the same thing. Converge before both ship (Anne back 6/19, Mike back 6/15).

## Go-live bar (Cameron's call, 6/12)

MVP pilots **after curriculum sign-off** — Mike + Brian verify the 205 seeded items match current field practice. No technical blocker beyond that. Open question for Mike: do managers sign off items **live in the field on a phone** or **batch at end of day**? The answer shapes the UX priorities (see UX audit). Videos (Austin L's library) can land during the pilot — the per-item video slot is already built.

## Companion docs (this folder)

- `audit-code-security.md` — graded code/security audit
- `audit-ux-workflows.md` — persona walkthroughs + top-10 UX fixes
- `replit-agent-handover.md` — implementation plan written for Mike's Replit agent
- Original review posted on [Cam's Board pulse 12266515680](https://einsteinmoving.monday.com/boards/549231076/pulses/12266515680)
