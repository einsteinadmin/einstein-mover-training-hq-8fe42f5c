# Curriculum: Mike's app vs. Anne's 60-day system
**Date:** 6/16/2026 · **For:** the merge meeting (Cameron · Paul · Brian · Matisen · Anne)
**Sources:** Mike's `lib/db/src/seed.ts` (205 items, live) · Anne's `performance-system-overview.md` + `council-reviews/` (last canonical update 6/3/2026 — **re-check the morning of the merge meeting; Anne may have moved it**).

## The headline: they're not two curricula. They're two halves of one.

The worry going in was "Mike and Anne each wrote a competing training curriculum; we have to reconcile them." **That's not what happened.** They built opposite halves and barely overlap:

- **Anne built the *measurement spine* — and explicitly left the content blank.** Her docs define *when* you're checked, *what gates you must clear*, and *what happens if you don't* — but her own overview calls the actual curriculum a **PLACEHOLDER** waiting on "the mover handbook, training videos, and training arc from Cameron." In her words: *"Improved Training Handbook = the curriculum. This IS Anne's 9 sprints."* She named the milestones; she never wrote what's in them.

- **Mike built exactly that missing content.** 205 real training items, the actual handbook digitized, the Perfect Move Checklist, 8 named mover-training sprints — but **no gates, no pace tracking, no pass/separate decision.**

So Mike didn't write a rival to Anne's system. **Mike filled the hole Anne flagged she was waiting on.** The merge isn't a reconciliation fight — it's a docking maneuver: Mike's content slots into Anne's calendar, and Anne's gates become the pace-tracking layer Mike's app is missing (which is already our build priority #1). That's the story for the room.

## Side-by-side

| | **Anne's 60-day system** | **Mike's app** |
|---|---|---|
| Owns | The *spine*: gates, cadence, decisions | The *content*: skills, checklists, sprints |
| Curriculum content | Placeholder ("9 sprints" named, not written) | ✅ 205 items, 4 stages, fully written |
| Sprint structure | 9 milestones (count only) | ✅ 8 named mover sprints + onboarding + lead |
| Check-in cadence | ✅ Day 7 / 14 / 30 / 45 / 60 | ❌ none |
| Pace targets | ✅ Wk1: 1–2 sprints · Wk2: 4+ · Day30: 6+ · Day60: all 9 | ❌ "minimum 5 days" as text only |
| 100-point overlay | ✅ 92→PIP, 90→terminate (training period) | ❌ none |
| Affirmative keep | ✅ Day-12 written "keep" (default OUT) | ❌ none |
| Peer review | ✅ binary "truck question" (story, not score) | ❌ none |
| Pass / separate | ✅ Day-60, decoupled from benefits cliff | ❌ none |
| Sign-off integrity | (not specified) | ✅ two-actor, server-enforced (a *gift* to Anne's spine) |
| Pillar framing | development engine first, gate second | gate-shaped (stages unlock sequentially) |

## Where they actually diverge (the real merge agenda)

Only a handful of genuine seams to settle — none are conflicts, they're joins:

1. **8 sprints vs. 9 — and what each is.** Mike's Stage 3 has 8 named mover sprints. Anne's "9" is a count with no names. First question for the room: **is Mike's 8-sprint list the canonical 9 (off by one), or does Anne's 9 span a wider arc** (onboarding → lead) than Mike's mover-stage-only sprints? My read: Anne's "9 sprints across 60 days" is broader than Mike's 8 *mover-training* sprints — Mike's onboarding (16 sections) and lead training are separate stages in his app but would count as "sprints/milestones" in Anne's day-targets. So the merge is really **mapping Mike's 4 stages onto Anne's day calendar**, then agreeing the milestone count.

2. **Mike's stages → Anne's calendar.** Mike gates by *sequence* (finish Stage 1 to unlock Stage 2). Anne gates by *date* (Day 7 = 1–2 done). These need a single mapping: e.g., "Onboarding = Wk1, Onsite Eval = Wk2, Mover Sprints 1–8 = through Day 45, Lead = post-60." That mapping is the core deliverable of the merge meeting.

3. **Anne's gates are Mike's missing module.** Day-7/12/30/45/60 check-ins, the 100-pt thresholds, the Day-12 keep decision, pass/separate — **none of this exists in Mike's app, and all of it is the pace-tracking module we already ranked #1 to build.** So Anne's spec is literally the build spec for module 1. That's the cleanest possible outcome: her design tells Mike's agent exactly what to build.

4. **The binary "truck question" peer survey** — Anne's, not in Mike's app. A small add, but it's a gate input, so it belongs in the pace module.

5. **Two-actor sign-off is a gift going the other way.** Anne's spec didn't have a tamper-proof sign-off mechanism; Mike's does. It should become part of the merged standard (and extend to every gate — the integrity fix from the review).

## What the merge meeting actually decides

- **Map Mike's 4 stages onto Anne's day-targets** (the one real design task).
- **Lock the milestone count** (is it 8, 9, or N once onboarding/lead are counted).
- **Adopt Anne's gates as the spec for Mike's pace-tracking module** (Day-7/12/30/45/60, 100-pt, keep/pass/separate).
- **Adopt two-actor sign-off as the standard** and extend it to every gate.
- **Sign-off lenses:** Matisen (tech/support ready) · Anne (gates + milestones match her system) · Brian (principle + operational mechanics) · Cameron + Paul (the call).

## Caveats (swim at your own risk)

- Anne's docs are dated 6/3; her actual sprint *names* were never written — so there's nothing to literally diff against Mike's 8. This comparison maps *structures*, not item-by-item. Re-confirm with Anne live before locking.
- Mike's "205 items" includes informational/context items, not only graded skills — the real "skill count" is lower. Don't treat 205 as 205 testable competencies.
