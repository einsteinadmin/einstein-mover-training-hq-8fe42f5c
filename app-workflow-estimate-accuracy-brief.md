# Field Mover App (iPad) — UX Redesign Workflow + Estimate-Accuracy Tie-In

**Status:** Idea log (Cameron brain-dump 6/4/2026) — to become a build workstream
**Owner:** Cameron + Matisen (build) / Mike + Brian (field)
**Subject:** The **current** Einstein Mover field app on iPad — NOT the long-term "Mover Super App" (`Supporting Projects/Matisen - IT/Mover Super App/`, a separate, possibly-not-long-term concept).
**Related threads:** #22 Estimate Accuracy · #23 Quote Funnel · #16 Website Redesign
**Code ref:** `Supporting Projects/Matisen - IT/System Architecture/repos/einstein-mobile-main`

---

## What the current field app actually does (Cameron's description)

The live iPad app movers use today:
1. Mover **checks in** on the iPad in the morning.
2. Opens the **Einstein Mover app** → sees **special instructions**: who the customer is, what to load in their truck.
3. App **routes them to their truck**.
4. **Walkthrough checklist.**
5. Do the **proposal** → **send the email proposal to the client**.
6. **Clock tracking** — *this is where the timestamps already live.*

So the timestamp infrastructure the Estimate Accuracy theme (#22) needs is partly **already in this app**. The redesign is about sharpening the UX *and adding the data-capture steps* that feed the estimate-accuracy learning loop.

> **Gating input from Cameron:** screenshots of the existing field-app screens + UX. We work off those to build the Current side of the workshop. (Same pattern that seeded the Quote Funnel Workshop #23 from quote-form screenshots.)

---

## Current app — reverse-engineered from the repo (6/4/2026)

Repo: `github.com/Einstein-Moving/einstein-mobile` · local copy committed in workspace at `Supporting Projects/Matisen - IT/System Architecture/repos/einstein-mobile-main`. Stack: **Expo / React Native, expo-router file-based routing, iPad-targeted** ("Einstein Mobile Companion"). Screen map read straight from `app/`:

| Route | Screen | Maps to Cameron's flow |
|---|---|---|
| `login.tsx` | Google sign-in | Morning check-in |
| `(app)/dashboard.tsx` | Moves + trucks list | "Who's the customer / what to load" + routes to truck |
| `(app)/[activeMove]/(clientInfo)/` | index · **inventory** · **alerts** | Customer details + load instructions |
| `(app)/[activeMove]/(todo)/` | **ArrivalTasks · Waivers · Departure** | Walkthrough checklist + the **clock/timestamps** |
| `(app)/[activeMove]/supplies` + `(supply)/` | overview · **inventory** · **checklist** | Truck inventory / supplies count |
| `(app)/[activeMove]/proposal.tsx` | Proposal PDF | Build + **email proposal to client** |
| `(app)/[activeMove]/invoice.tsx` | Invoice + signatures | Close-out / payment |
| `(app)/[activeMove]/movers.tsx` | Crew on the job | — |

**Two findings that change the build math:**

1. **Photo capture already exists.** `views/activeMove/hooks/useUploadImage.tsx` already implements `takePhoto()` (camera), `pickImage()` (library), permission handling, base64 upload — with a "Take a Photo / Choose from Library" prompt. → **In-app truck photos are an *extension of existing infra*, not a from-scratch build.** Big de-risk on the net-new idea.
2. **Timestamps exist — but the model has moved on (⚠️ re-verify with Matisen).** The repo I read (mid-2025) shows departure/arrival running through Zustand stores (`useDepartureStore`, `useArrivalTasksStore`, `useTodoStore`) with an `ActivityState` enum (`MOVE_FINISHED`) — i.e. an **await/sequential** model. **Matisen (6/5) says this is now stale:** the app no longer runs sequentially — it **syncs to the backend on every timestamp/event**, so a move's state is recoverable from any iPad mid-job (app crashes → pick up on another device, pull move status, run the contract from there). Net: timestamps aren't just present, they're now a **persisted, event-level backend stream with cross-device recovery** — which is *better* for estimate accuracy (every segment mark is a discrete, durable event, exactly the granular capture the rubric needs). But **do not build on the timestamp/sync layer off the Figma or the repo — confirm the current backend-sync logic with Matisen first.**

**Net:** the app already has inventory, a checklist, proposal+email, invoice/signatures, departure/arrival timestamps, AND camera upload. The estimate-accuracy additions are mostly *extensions of things already present* — the redesign is about **adding the right capture steps and sequencing them well**, not green-fielding capability. (Screenshots still useful for visual fidelity of the workshop's Current side, but the structure is now known.)

---

## Figma access + export (6/5/2026) — DONE

**Wired up via Figma REST API.** Token stored in `~/.zshrc` as `FIGMA_TOKEN` (full-access, 90-day exp — treat READ-ONLY; never write without Cameron's say-so). Two files:
- **Design:** "EMMA - Mobile" — file key `TkEyfhAbMpB9qMckQ3lrwY` (lastModified 2025-05-27). Many dated snapshot pages; **canonical complete flow = page "Shared on 03.02.2025 - V4"** (newest full version; "Updated 29.01.2026 - backup" is V3). Per Cameron ~95% accurate to the live app.
- **UX logic:** "EMMA - UX Logic" FigJam board — file key `K16vMh5XpL3Mby59ThYzcW`. Canonical = page "Mobile App - v4" → section **"SUGGESTED FLOW OF APP - V4"** [89:3260] (the navigation diagram).

**Exported** (in `figma-export/`):
- `screens/` — 20 canonical V4 PNGs @2x: login · homescreen · move-details · arrival-tasks · alerts · inventory · departure · waivers(empty/filled) · proposal · sign-proposal · invoice · confirm-on-call · confirm-completion · supplies · adding-supplies · movers · adding-movers · search-mover.
- `flows/` — `design-V4-flow.png` (screens laid out with connectors) + `uxlogic-V4-flow.png` (the FigJam navigation logic).
- Raw JSON: `design-depth2.json`, `uxlogic-depth2.json` (full node trees — usable to reconstruct layout/prototype links precisely).

**Fidelity confirmed:** homescreen render shows truck selector, "Today's overview" (Alerts/Moves/Locations/Packing-jobs counts), orange **START DAY** button, ACTIVE/NEXT-MOVE/FINISHED cards — matches `dashboard.tsx` 1:1, correct Einstein orange. **Design + repo + Figma now triangulate the same app.**

> Note: design file dates to early-2025 (V4); repo code is mid-2025. Cameron's ~95% caveat holds — workshop "Current" side should be spot-checked against any newer screens before we lock proposed changes.

> **⚠️ Matisen correction (6/5):** the Figma UX-logic flow is **generally right for screen-to-screen flow, but the timestamp/sync layer is stale** (that mapping came from initial discussions 1+ yr ago, on an await/sequential model). The live app is now **backend-synced on every event** with cross-device move recovery. **Trust Figma + repo for general flow; treat all timestamp/sync behavior as unverified and re-confirm the current backend logic with Matisen before designing on it.** This is the gating dependency for the segmentation work (ties #22's "de-risk the app timestamp timeline with Matisen").

---

## Verified end-to-end flow (sweep 6/5 — repo router + UX-logic V4 board + screens)

Cross-checked all three sources. The real usage sequence (not just tab order):

**Tab bar (persistent, from `DetailsRootLayout`):** Client Info · TO DO · Proposal · Movers · Supplies · Invoice.
**TO DO stack (`TodoStackLayout`):** Move Details (index) → Waivers → Arrival tasks → Departure → Proposal.

**Temporal flow (from the UX-logic "SUGGESTED FLOW OF APP – V4" board):**
1. **Clock in / Start Day** — mover picks their jobs, morning truck check.
2. **Today's Overview (Home)** — alerts / moves / locations / packing counts; START DAY.
3. **Move Details** (hub) — customer, address, **View Directions**, **START PROPOSAL**; live **Syncing / SYNC NOW / Last synced** indicator (confirms backend event-sync). Shows the ordered checklist: Arrival tasks → Waivers → Departure.
4. **Drive to location** → *1st/2nd timestamps* (depart, arrive).
5. **PROPOSAL — "Initial Proposal Pricing"** *(on arrival, BEFORE the walk-through)* — estimate breakdown (time traveled, movers × rate, hours, travel charge, totals), **Confirm Proposal → starts the movers clock** (*3rd timestamp*). Has a **Reestimate Proposal** button.
6. **Sign Proposal** (customer signs).
7. **Arrival Walk-Through Checklist** ("Arrival tasks") — *after* the proposal.
8. **Waivers / Damage Claim** — liability waiver, damage items + photos, shipper + carrier signatures.
9. **Movers** (start job, clock, breaks, on-call) + **Supplies / Load** + **Client Inventory**.
10. **Departure Walk-Through Checklist** → *5th timestamp* (depart for next).
11. **Reestimate Proposal** — discounts, gratuity, damage claim form, final sum.
12. **Invoice / Payment** (NFC, sync).
13. **Confirm Completion** — FINISH → confirm → **sync with backend**.
14. **Finish Day** — clock out (*6th timestamp*), **end-of-day reports / truck reset pics**.

### ⭐ Big finding: much of the estimate-accuracy vision is ALREADY in the UX board (just not built)
The year-old UX-logic board already envisions:
- **6 timestamps** across the move ("1st…6th time stamp", "Auto START/STOP mover clock times") → the **segmentation** idea is already conceived.
- **"START PROPOSAL turns into REESTIMATE PROPOSAL" / "PRESS REESTIMATE PROPOSAL"** → the **walkthrough-as-second-quote / re-estimate** is already in the flow (an existing button, not a net-new idea).
- **"End of day reports / truck reset pics"** → the **truck reset photo** is already imagined.
- **"Add damage claim form that is required"** → waivers/damage capture.

**Implication for the proposed changes:** several of Cameron's ideas aren't net-new screens — they're **sharpening / wiring-for-accuracy of capabilities the UX already imagines** (timestamps → clean segmentation; reestimate → AI video inventory; truck reset → in-app + RAG). The genuinely net-new pieces are the **AI/data layer** (auto-inventory from video, RAG volume read, post-move variance reasons, dispatch grading), not the capture moments themselves. The proposed-changes set should be reframed against this (next step).

### Screens now in the workshop (corrected order)
Login · Home/Start Day · Move Details · Client Inventory · **Proposal (arrival)** · Sign Proposal · **Arrival Walk-Through** · **Waivers/Damage** · Movers/Clock · Supplies/Load · *Truck Photo (proposed)* · Departure W-T · Invoice/Payment · Confirm Completion · *Post-Move Report (proposed)* · Backend/Dispatch.
Swapped in the real **Move Details** (active hub, 276:5953) and real **Proposal** (276:7832, was the empty-state frame); added **Waivers/Damage** (09).

---

## ⭐ Direction (6/5 PM) — full HTML rebake + clickable + flow-view (parked)

**Decision (Cameron):** stop using Figma PNG screenshots as the screens — **rebuild every screen in HTML** (~95–98% fidelity is plenty for a workshop; pixel-perfection isn't worth losing editability). Rationale: this tool will host *many and significant* UX changes over time (see the 50+ item Move Contract App Backlog), so inline-editable HTML beats static screenshots. Figma demoted to **visual reference** (`figma-export/ref/` = 900px copies to build against). Pattern file `_shared/workshop-tool-pattern.md` updated with this principle.

**Build requirements:**
- **Cover every page** — cross-reference the Figma design ("Shared on 03.02.2025 – V4" page) + the UX-logic flow board so no screen/modal is skipped (incl. sub-screens: Client Info, Alerts, Adding Movers, Search Mover, Adding Supplies, Confirm On-Call, Reestimate variant).
- **Clickable navigation** — make the screens navigate like the real app (tap START PROPOSAL → Proposal; checklist row → that screen; bottom tab bar works). Click-through prototype, not static.
- **Inline proposed changes** — native elements rendered in-flow (push content down), per-change 👁 toggle + Keep/Deny, dashed "Proposed" ring. No more absolute-positioned post-it overlays.

**Full screen inventory to build (from V4 design + flow):**
Login · Home/Today's Overview (Start Day) · Move Details (hub) · Client Info · Alerts · Client Inventory · Proposal (Initial Pricing) · Reestimate Proposal · Sign Proposal · Arrival Walk-Through · Waivers/Damage (empty+filled) · Movers/Clock · Adding Movers · Search Mover · Supplies/Load · Adding Supplies · Departure Walk-Through · Invoice · Confirm On-Call · Confirm Completion (+complete modal) · *Truck Photo (proposed)* · *Post-Move Report (proposed)* · Backend/Dispatch (context).

**🔮 PARKED — future "UX Flow View" second tab (Cameron, 6/5, NOT now):** a whole other tab/mode that pulls up the **app's UX flow as a navigable map** — move around the flow, make adjustments, and see proposed changes at the flow level (a flow-diagram / whiteboard view, complementary to the screen-by-screen workshop). Keep in memory; build in a later session/phase. (Mirrors the Figma UX-logic board, but interactive + change-aware.)

---

## The build frame

Build a **Field App UX Workshop** tool — same pattern as the **Quote Funnel Workshop** (#23) and **Sales Email Workshop** (#19): Current ↔ Proposed, step-by-step, with per-change toggles, so proposed UX changes are concrete and reviewable *before* handing them to Matisen/Fabian to build. Screenshots of the current app = the Current side.

---

## Proposed additions to the field-app flow (Cameron's brain-dump)

**Estimate-accuracy spine (mostly already anticipated in #22):**
- **Segmentation timestamps** — load start → load complete → drive → unload start → unload complete. (Builds on the clock tracking that's already in the app.) Lets us de-mix into an additive rubric `load + drive + unload`.
- **Walkthrough → AI auto-inventory.** Add a **video walkthrough at move start**; model builds an inventory list, compares to the initial estimate, and shows **how the estimate should update in real time, on the spot.** This is the walkthrough-as-second-quote, AI-assisted. (The app already has a walkthrough checklist step — this upgrades it.) *Build path note (6/11 scout): start with Claude-vision-on-frames — no infrastructure, testable in an afternoon; if/when it proves out and needs real-time or scale, `supervision` (roboflow/supervision, 40K-star open-source CV toolkit for detection/tracking on video) is the standard to build on. For Matisen at the working session.*
- **Post / inter-move report** — after every move (or triggered on **±30% over**): customer satisfaction, "what was unexpected," and an over/under **reason dropdown** (went long / shorter walk / more stuff / extra stop / etc., + an "estimate was accurate" option). Turns misses into labeled training data.

**Net-new (not captured anywhere before — the photo/dispatch layer):**
- **In-app truck photos.** At the **end of each load-up segment** (before closing the door / heading to unload / starting a pack / driving to the next stop) → **photo of the back of the truck** (doubles as the load-complete timestamp marker). At **end of move** → reset-truck photo.
  - *Today this happens informally:* movers post truck-reset photos into **Slack channels keyed by truck** — that's how managers run dispatch/QC now. The idea is to pull that capture **into the app.**
- **RAG image database** of truck-load photos → (a) "how much actually made it in" volume signal, (b) continuous wrap/load **QC** (today QC only comes from on-site evals).
- **EMMA dispatch center** — push app data into a dispatch view: where all trucks are + incoming photos. Replaces the truck-keyed Slack channels.
- **AI grading / triage** — auto-grade walkthroughs + photos to assist dispatch QC, run the QC audit, and **flag the photos that need human attention / coaching** (review by exception).

---

## How much of this is the estimate-accuracy problem? (Cameron's question)

Split it into **two flywheels sharing one app build** — they're not the same lever:

| Feature | Estimate accuracy? | What it buys |
|---|---|---|
| Load/drive/unload **timestamps** | ★★★ Core | The de-mixing fuel. Highest value for the rubric. Partly already in the app. |
| **Walkthrough re-quote** (+ video auto-inventory) | ★★★ Core | Biggest accuracy lever — best info, least committed labor. |
| **Post/inter-move report** (variance + reason) | ★★★ Core | Labeled training data; nominates new quote-form questions. |
| **Truck-load photos → RAG volume signal** | ★★ Supporting | Objective "how much made it in" cross-check. Secondary to timestamps + walkthrough. |
| **Truck photos for wrap/load QC** | ☆ Adjacent | Quality control + coaching, *not* accuracy. Valuable on its own (continuous vs. spot evals). |
| **EMMA dispatch center + AI grading** | ☆ Adjacent | Dispatch automation / efficiency. Shares the data, different ROI story. |

**Honest read:** the timestamps + walkthrough + post-move report are the estimate-accuracy spine (and largely already scoped in #22). The new photo/RAG/dispatch/AI-grading ideas are **two things bolted onto the same data pipe**: (1) a genuine *secondary* accuracy signal (volume-in-truck), and (2) a separate, valuable **QC + dispatch automation** play.

For selling it: pitch estimate accuracy on the proven levers (timestamps + walkthrough + report); frame photos/RAG/dispatch as a **second flywheel** the same build unlocks nearly for free (continuous QC replacing spot evals + dispatch automation). Two ROI stories, one app — don't blend them or the accuracy case gets fuzzier, not stronger.

**Design principle for the workshop:** every new step taxes a mover's hands mid-job. Capture must do double duty (the truck photo *is* the load-complete timestamp; the walkthrough video *is* the re-quote) — never a standalone chore, or compliance drops and the data rots.

---

## Next steps

1. ✅ **DONE — Figma + repo pulled** (20 screens + flow diagrams + node JSON + repo code). Screenshot/structure gathering is complete.
2. **Build the Field App UX Workshop** (Current ↔ Proposed, per-step toggles) using the real V4 screens as the Current side, seeded with the additions above. *Safe to build the screen-flow + UX-change layer now* — that part of the Figma is reliable.
3. ⚠️ **GATE before designing the timestamp/segmentation steps:** re-verify the current **backend-sync logic** with Matisen (event-synced, cross-device recoverable — not the stale await/sequential model in Figma/repo). Don't lock any timestamp-dependent proposed change until then.
4. Keep the **accuracy spine vs. QC/dispatch second-flywheel** split explicit in the tool.
5. De-risk with Matisen: which additions the app build can carry + on what timeline (the #22 gating dependency — no app, no Q3 theme fuel).
