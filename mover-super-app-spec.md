# Mover Super App — MVP Specification

**Version:** 3.1 (March 22, 2026)
**Author:** Cameron Brown + Albert
**For:** Matisen Harper, Fabian, Mike Vandenbroader, Sergio Buenrostro
**Status:** Draft — prototype built, awaiting Cameron/Matisen/Mike alignment

---

## How This Spec Was Built

Three inputs were merged to create this:

1. **Sergio's Houston Performance Dashboard** (live app, 19 tabs) — proven feature set, real mover data, strong visual design. The floor. What movers actually want to see.
2. **Clean-sheet first-principles design** — designed from scratch around mover psychology, forward-looking simulators, interactive what-if tools, mobile-first UX. The ceiling.
3. **Mike's Digital Training Blueprint** — 8-sprint Copilot training + Lead Training track with video verification, photo proof, praise requirements, and manager approval workflows. The training engine.

The merged design takes Sergio's features, adds the clean-sheet's interaction model, and plugs in Mike's training system.

---

## The User

Marcus is 22. He makes $18.25/hour moving furniture in the Texas heat. His phone is his primary computer. He processes information through color, size, and pattern. He cares about three things at work: **how am I performing, am I in trouble, and what's next.**

Questions that live in the back of his mind every shift:

- "How am I doing compared to the other guys?"
- "How many points do I have? When does my next one fall off?"
- "What's my bonus looking like?"
- "What am I doing tomorrow?"
- "Can I afford to call in on Friday?"
- "What do I need to do to get promoted?"

---

## Design Principles

**1. Performance first, money second.** The home screen shows how you're performing across all dimensions — not just the ones tied to bonus. Safety, quality, reviews, attendance all matter even when they don't directly impact comp.

**2. Forward, not just backward.** Static dashboards show history. This app also shows the future. Simulators let movers model "what happens if..." — but the foundation is solid performance visibility.

**3. Color IS the information.** Green = good. Orange = watch it. Red = act now. A mover should glance for 2 seconds and know their world.

**4. Yours, not theirs.** Language is first-person: "your streak," "your money," "your next level." Never "compliance," "violations," or "metrics." This is a personal tool, not a surveillance system.

**5. Thumb-zone, phone-first.** Navigation at the bottom. Key interactions in the center-bottom of the screen. Nothing critical in the top-left corner (hardest to reach one-handed).

---

## Access Model

**MVP: Open access** (same as Sergio's current app). Mover selects their name from a searchable list. No login friction. Gets adoption moving fast.

**V2: PIN + biometric.** When the app goes company-wide and shows compensation data, add a 4-digit PIN (chosen by mover, stored locally). Face ID / fingerprint as an option. No email required. PIN reset through the branch manager in person.

---

## The App: 8 Screens

### Screen 1: The Pulse (Home)

**What it answers:** "How am I doing right now?"

This is the mover's daily check-in. Performance stats lead. Money is visible but not dominant.

**Layout:**

```
┌──────────────────────────────────┐
│  Hey Marcus      South Austin    │
│  Lead Mover  •  1 yr 4 mo       │
│                                  │
│  ┌──────┐ ┌──────┐ ┌──────┐    │
│  │ #4   │ │  87  │ │$0.45 │    │
│  │Branch│ │Safety│ │ DPH  │    │
│  │ Rank │ │Score │ │      │    │
│  └──────┘ └──────┘ └──────┘    │
│  ┌──────┐ ┌──────┐ ┌──────┐    │
│  │ 4.8★ │ │ 1.5  │ │ 85%  │    │
│  │Review│ │Call-in│ │Quote │    │
│  │ Avg  │ │Points│ │ Acc  │    │
│  └──────┘ └──────┘ └──────┘    │
│                                  │
│  ┌────────────────────────────┐  │
│  │ 💰 Projected Bonus: $384  │  │
│  │    +$22 from last week ▲  │  │
│  │    Tap to simulate →      │  │
│  └────────────────────────────┘  │
│                                  │
│  ┌────────────────────────────┐  │
│  │ 📅 Today: 2 moves         │  │
│  │ 8:00 AM - 3BR, Leander    │  │
│  │ 2:00 PM - 1BR, N. Austin  │  │
│  └────────────────────────────┘  │
│                                  │
│  [Pulse] [Simulate] [Board]     │
│  [Schedule] [More]              │
└──────────────────────────────────┘
```

**Six stat tiles** across two rows — these are the mover's key performance indicators:
- **Branch rank** (Einstein Games composite) — color: neutral
- **Safety score** (Samsara) — green 80+, orange 60-79, red <60
- **DPH** (damages per hour) — green below $1.23, orange at target, red above
- **Review average** (Google reviews) — star display
- **Call-in points** — green 0-2, orange 2.5-4, red 4.5+
- **Quote accuracy** — reliability percentage

**Bonus card** below the stats — shows projected quarterly bonus with trend. Tapping opens the Bonus Simulator.

**Today's schedule** at the bottom — compact view of the day's moves.

**Data:** EMMA `einstein-games/me`, `samsara-safety/me`, `damages/me`, `reviews/me`, `mover-attendance/me`, `quote-accuracy/me`, `assignment/me`, `bonus-projection/me`.

**Why this works:** Performance stats lead because not everything maps cleanly to bonus — safety culture, reviews, and praises matter independently. The bonus card is prominent but not the centerpiece. The mover sees their full picture, then can drill into any dimension.

---

### Screen 2: My Stats (Deep Dive)

**What it answers:** "Give me the full breakdown — what's going well, what needs work?"

This is the expanded version of the Pulse tiles. Tap any tile on the Pulse → lands here on the relevant section.

**Sections (scrollable cards, each expandable):**

**Performance Rings** (top, signature visual — borrowed from clean-sheet design):
Four concentric ring segments that fill based on performance:
- Outer: Safety (driver score)
- 2nd: Quality (DPH)
- 3rd: Service (reviews + quote accuracy)
- Inner: Reliability (attendance)

Four full green rings = crushing it. One orange ring = that's what needs work. Visual health check in 1 second.

**Detail cards (below rings):**

| Card | Shows | Key Detail |
|------|-------|------------|
| **Safety** | Score (87), trend, 4-week sparkline, branch rank | Coaching tip: "Following distance improved. Mobile usage is next." |
| **Damages** | DPH ($0.45), damage-free streak (12 days), total $ this quarter | Goal threshold bar: "Under $1.23 = green" |
| **Reviews** | Star avg (4.8), count (22), most recent review | Tap to see all reviews |
| **Quote Accuracy** | Reliability % (85%), on-track/over/under breakdown | "7 jobs over estimate this quarter" |
| **Attendance** | Current points (1.5), next expiration date, history | Preview of Attendance Planner |
| **Onsite Evals** | Avg score, pass/fail record, last eval date | 90-day rolling average (from Eval Tracker) |

**Auto-generated coaching tips** at the bottom — specific, data-driven: "Your DPH has been under $0.50 for 6 straight weeks. If your driver score gets above 85, you'll be in the top 3." (Borrowed from both Sergio's app and the clean-sheet design — both independently came up with this.)

**Source of truth:** Sergio's "My Stats" page proved movers want this level of detail. The performance rings from the clean-sheet add a faster visual summary on top.

---

### Screen 3: The Simulator (Bonus + Attendance)

**What it answers:** "What's my bonus going to be? Can I afford to call in? What happens if...?"

This is the forward-looking feature Cameron specifically asked for. Two sub-tabs: Bonus and Attendance.

#### Tab A: Bonus Simulator

**The big number:** "$384" — projected quarterly bonus, large and bold.

**Formula breakdown:** Horizontal stacked bar showing where the bonus comes from:
- Safety contribution (blue)
- Attendance contribution (green)
- DPH contribution (orange)
- Review/quality contribution (gray)

**Interactive sliders** that update the bonus number in real time:

| Slider | What It Controls | Example Impact |
|--------|-----------------|----------------|
| Driver safety score | 0-100 scale | "+$23 if you hit 90" |
| Projected hours this quarter | Current → reasonable max | "520 hrs = $420, 480 hrs = $365" |
| DPH (damages) | Current → zero | "One more $200 damage = -$35 from bonus" |
| Review rate | Current → target | Impact on review contribution |

**Quick presets:**
- "Best case" — all metrics at personal best. The ceiling.
- "Current pace" — default view.
- "If I slack off" — metrics decline to average. The floor.

**Historical anchor:** "Last quarter you earned $310. You're tracking $74 higher."

**Key dependency:** This screen requires the bonus formula to be codified. Cameron + Paul decision. Without it, show a placeholder with the stat tiles but no interactive sliders.

#### Tab B: Attendance Planner (Call-Out Simulator)

**Speedometer gauge:** Semicircular gauge showing current call-in points:
- Green zone (0-2 pts): "You're clear"
- Orange zone (2.5-4 pts): "Watch it"
- Red zone (4.5-5.5 pts): "Danger zone"
- Black zone (6+ pts): "Termination risk"

**Point timeline:** Horizontal 90-day view:
- Gray dots = expired points
- Orange dots = active points
- Green dot with countdown = next expiration: "0.5 pts drop off in 12 days"

**The simulator buttons:**

| Button | Points Added | Color |
|--------|-------------|-------|
| "Late arrival" | +0.5 | Orange |
| "Call-out" | +1.0 | Darker orange |
| "No-show" | +1.5 | Red |

Tap one → gauge animates to the new balance. If it pushes into danger: "This would put you at 5.0 points. That's the danger zone."

**Date picker:** "What if I call in on [date]?" — impact changes depending on when points are set to expire.

**Clean projection:** "If you stay clean, you'll be at 1.0 points by April 15."

**Key dependency:** Rolling window policy (90 days? 6 months?). Cameron + Anne decision.

---

### Screen 4: The Board (Leaderboard + Games)

**What it answers:** "Where do I stack up?"

Borrowed heavily from Sergio — his leaderboard is already proven. Enhanced with category filtering from the clean-sheet design.

**Your position (top):** "#4 of 23" with composite score. You're highlighted in the list below.

**Leaderboard list:** Ranked list of all branch movers:
- Rank | Avatar | Name | Score | Trend arrow
- Top 3 get medals (gold/silver/bronze)
- Your row highlighted with orange border

**Category tabs** (horizontal scroll):
Overall | Safety | DPH | Reviews | Quote Accuracy | Attendance

A mover might be #12 overall but #2 in reviews. Let them find their strengths.

**Branch vs. Branch:** "South Austin is #3 of 10 branches." Team pride.

**Quarterly MVP spotlight** (from Sergio): Banner celebrating the MVP with stats.

**Damage-free streak counter** (from Sergio): "81 days" — branch-level gamification.

**Road Warriors top 3** (from Sergio): High miles + high safety score.

**Additional views from Sergio worth keeping:**
- Compare Movers (side-by-side, manager-facing)
- Mover Games composite scoring table
- Quote Accuracy partner synergy analysis (which crew pairings work best)

---

### Screen 5: My Schedule

**What it answers:** "What am I doing today?"

**Today view (default):**
- Time, move type (3BR house), location (city-level), crew (avatar + names), truck assignment, estimated duration
- Travel time between moves
- If clear: "No moves today."

**Week view (tab toggle):**
- Mon-Fri compressed: move count + hours per day
- "Mon: 2 moves (8 hrs) | Tue: OFF | Wed: 3 moves (10 hrs)..."

**Hours tracker (bottom bar):**
- "This week: 32 hrs. Projected: 42 hrs."
- "This quarter: 410 hrs."
- Connects to Bonus Simulator — more hours = bigger bonus

**Data:** EMMA `assignment/me`, `workday_truck_confirmed_moves`.

---

### Screen 6: Level Up (Career + Training)

**What it answers:** "What do I need to do to get promoted and make more money?"

This screen merges the clean-sheet's career path visualization with Mike's training accountability system.

#### Career Path (top section)

Vertical trail with nodes at each level:

| Level | Pay | Progression |
|-------|-----|-------------|
| Copilot | Starting rate | (completed) |
| Junior Lead | $18.50/hr + $1.75 bonus | |
| Lead | $19.00/hr + $2.00 bonus | |
| Senior Lead | $19.50/hr + $2.25 bonus | |
| Master Lead | $19.50/hr + $3.00 bonus | |
| Lead General | $19.50/hr + $3.75 bonus | "Genius Status" |

Current level = glowing dot. Next level highlighted with progress bar. Each level shows the pay bump: "+$1.25/hr" in green.

**Requirements checklist for next level:**
- "500 hours logged" — 410/500 (82%)
- "Safety score above 80 for 3 months" — checkmark
- "Complete SMART Driving Program" — 6/8 modules
- "Copilot Training complete (8 sprints)" — progress
- "Manager recommendation" — pending or confirmed

#### Training Programs (below career path)

**Two training tracks (tabs):**

**SMART Driving (8 modules):**
Module cards with title, duration (~15-25 min), completion %. Links to SMART Driving Habits Replit app. Modules:
1. Introduction to SMART Driving
2. Scan Your Surroundings
3. Maintain Your Distance
4. Anticipate Ahead
5. Recognize Your Position
6. Track Everything
7. SMART Habits Summary
8. Safe Backing Protocols
Plus 2 coming soon: Understanding Samsara, Einstein Truck Basics

**Copilot Training (Mike's 8-sprint program):**
Sprint cards with progress. Each sprint enforces (per Mike's blueprint):
- Training videos watched to 95%+ (can't skip forward)
- Photo proof of completed skills (submitted + manager approved)
- Digital praise submission (embedded, ECP structure)
- eNPS survey (Sprints 3 and 8)
- Manager sign-off (checklist-gated)

Sprints are locked — can't advance without manager approval. Sprint topics include truck checks, home prep, packing, couch handling, TVs, customer interaction, etc.

**Lead Training (unlocks after Copilot complete):**
Separate track with admin/customer-facing skills, role-play requirements, and deeper manager involvement.

#### Badges (bottom)

Collection of earned achievements:
- "Damage Free 30 Days"
- "5-Star Streak" (3 consecutive reviews)
- "Perfect Attendance" quarterly
- "SMART Driver Certified"
- "Copilot Training Complete"
- Sprint completion badges

Badges show on leaderboard profile.

---

### Screen 7: The Feed (Recognition)

**What it answers:** "Does anyone notice my work?"

Unified recognition feed — everything positive in one place.

**Feed items (newest first):**
- **Customer reviews:** Stars, customer name, review text, crew attribution
- **Peer praises:** Core value tag, who submitted, message (from Legends/praise system)
- **Manager recognition:** Formal shoutouts
- **Auto-milestones:** "30-day damage-free streak!" / "100th move at Einstein!"

**Filters:** All | Reviews | Praises | Milestones

**Stats summary (top):** "22 reviews this quarter (4.8 avg) | 3 praises | 2 milestones"

**Shareable cards (V2):** Branded Einstein card with review text + mover name, shareable to social media. Not MVP — build later.

**Data:** EMMA `reviews/me`, `praises/me`, plus milestone triggers from performance thresholds.

---

### Screen 8: Branch Home (Manager + Team View)

**What it answers:** "What's happening at my branch?"

This is Sergio's branch dashboard — the team-level view. Content includes:

- **Announcements** — company news, EMMA updates
- **Events calendar** — paydays, birthdays, work anniversaries, holidays, training days
- **Quarterly focus** — current theme (Drivers ED) with branch safety gauge
- **Road Warriors top 3** — high miles + high safety score
- **Fleet safety alert log** — speeding, mobile, braking counts
- **Damage-free streak** — branch-level counter
- **Recent customer reviews** — rotating feed with star ratings
- **Team praise** — latest praise with core value
- **Quarterly MVP** — stats spotlight

This screen is both mover-facing (team awareness, celebrations) and manager-facing (branch health at a glance).

---

## Notification Strategy (Positive Only)

| Notification | When | Purpose |
|-------------|------|---------|
| Morning schedule | 6:30 AM workdays | "2 moves today. First: 8AM, 3BR Leander." Daily hook. |
| New review | Real-time | "A customer left you a 5-star review!" Dopamine. |
| Rank change | Monday morning | "You moved up to #3 this week." Weekly narrative. |
| Point expiration | 3 days before | "0.5 points drop off Thursday." Relief. |
| Bonus update | Bi-weekly | "Projected bonus: $402 — up $18." Money reinforcement. |
| Training nudge | Weekly (if incomplete) | "2 modules from SMART Certified." Gentle, capped. |

**Never push:** Damages, safety violations, negative events. Those are visible in-app but pushing bad news creates negative association. The app should feel like a reward.

---

## Existing Apps: What Connects, What Stays, What Goes

| App | URL | Role in Super App |
|-----|-----|-------------------|
| **Sergio's Dashboard** | `einstein-movers-performance-dashboard-74375884848.us-west1.run.app` | **Reference design.** Feature set, visual patterns, and proven UX inform every screen. Not forked directly — rebuilt with EMMA live data underneath. |
| **SMART Driving Habits** | `asset-manager--regionalteam.replit.app` | **Bridge for V2.** 8 modules feed into Level Up training tab. Expose `GET /progress/{employeeId}`. |
| **Onsite Eval Tracker** | `onsite-eval-tracker.replit.app` | **Data source.** 90-day scoreboard data feeds My Stats. Eval data already syncs to EMMA. |
| **Legends Manager** | `einstein-legends-manager--cameron-b.replit.app` | **Low priority.** Could feed The Feed later. |
| **Friday Dispatch** | `friday-dispatch-editor.replit.app` | **No connection.** Stays standalone. |
| **Digital Handbook** | Not built | **Build into Level Up.** Mike's blueprint defines the content + accountability system. Don't create another island. |

---

## Data Architecture

**Existing EMMA endpoints needing RBAC changes:**
```
GET /api/v3/einstein-games/mover/{id}     → Add mover self-view
GET /api/v3/mover-attendance              → Add mover self-view
GET /api/v3/assignment                    → Add mover filter
```

**New endpoints needed:**
```
GET /api/v3/employee/me                   → Profile
GET /api/v3/einstein-games/me             → Own scores
GET /api/v3/mover-attendance/me           → Own attendance + expiration dates
GET /api/v3/assignment/me?date=YYYY-MM-DD → Own assignments
GET /api/v3/damages/me                    → Own damage report + DPH
GET /api/v3/quote-accuracy/me             → Own accuracy stats
GET /api/v3/reviews/me                    → Reviews mentioning mover
GET /api/v3/praises/me                    → Praises for mover
GET /api/v3/bonus-projection/me           → Projected bonus (V2, needs formula)
GET /api/v3/onsite-evals/me              → Own evaluation history
```

**Total API work: ~2-3 weeks of Fabian's time.** Mostly RBAC wrappers + point expiration logic + bonus projection (once formula exists).

---

## Dependency Chain

```
DECISIONS (Cameron — before development)
├── Bonus formula: inputs, weights, payout structure (Cameron + Paul)
├── Call-in point rolling window: 90 days? 6 months? (Cameron + Anne)
├── Build approach: Sergio consults? Cameron prototypes? (Cameron + Matisen)
├── Pilot branch: Houston (familiar) or South Austin? (Cameron)
├── Comp visibility: can movers see each other's pay? (Cameron + Paul)
└── Sergio's role: consultant, part-time contributor, or recognition only?

PHASE 1: "See Your Own Data" (4-6 weeks)
├── EMMA API: /me endpoints + RBAC changes (Fabian, weeks 1-2)
├── Frontend: Pulse + My Stats + Schedule (weeks 2-4)
│   Cameron prototypes in HTML/CSS/JS, Sergio's app as visual reference
├── Open access (name picker, no auth)
└── Pilot at one branch (week 5-6)

PHASE 2: "Simulate Your Future" (3-4 weeks, can overlap)
├── Bonus Simulator (requires formula — Cameron + Paul blocker)
├── Attendance Planner (requires rolling window — Cameron + Anne)
├── SMART Driving API bridge
└── These are the features that differentiate from a static dashboard

PHASE 3: "Compete + Grow" (3-4 weeks)
├── The Board (leaderboard + games)
├── Level Up (career path + training)
│   └── Mike's Copilot Training system integrated here
├── The Feed (recognition)
├── Branch Home (Sergio's team dashboard)
└── Badge system

PHASE 4: "Scale + Secure" (2-3 weeks)
├── Deploy to all 10 branches
├── Add PIN/biometric auth
├── Push notifications (positive only)
└── Shareable review cards (V2)
```

---

## Engagement & Gamification System (Added 3/22, Session 19)

Inspired by Duolingo's engagement model. Core insight: **streaks create the habit, milestones create the dopamine, badges create the identity, leaderboards create the competition.**

### Streak Hierarchy

| Tier | What | Visibility | Purpose |
|------|------|-----------|---------|
| **Tier 1: THE Streak** | Shift streak (consecutive scheduled shifts worked) | Home screen, always visible, animated flame | Drive attendance — the behavior that matters most |
| **Tier 2: Active Streaks** (max 4) | Damage-Free Days, On-Time Streak, 5-Star Review Streak, Quiz Streak | My Stats screen | Reinforce secondary behaviors without overwhelming |
| **Tier 3: Badges** (unlimited) | Earned once, collected forever | My Stats badge showcase | Create identity and long-term goals |

**Design rule:** Never show more than 4 active streaks. Badges can be infinite.

**Streak milestones** (celebration screen triggers): 7 shifts, 14 shifts, 30 shifts, 60 shifts, 90 shifts, 180 shifts, 365 shifts. Each milestone shows the Einstein mascot + encouraging copy + "LET'S GO" CTA.

### Badge System (18 badges in v1, expandable)

**Earned badges** (6 in Marcus's demo):
- On Fire (7-day shift streak)
- Bubble Wrap (30 damage-free days)
- Fan Favorite (3 five-star reviews in a row)
- SMART Driver (complete 6 SMART modules)
- Hype Man (submit 5 peer praises)
- Quiz Whiz (7-day quiz streak)

**In-progress badges** (6, showing progress):
- Inferno (30-day shift streak)
- Money Moves (hit max quarterly bonus)
- Stair Master (50 third-floor moves)
- Centurion (100 moves at Einstein)
- Iron Hands (60 damage-free days)
- Podium (finish top 3 in branch)

**Locked badges** (6, aspirational):
- Perfect Quarter (zero call-outs + zero damages + all 5-star)
- Copilot Grad (complete all 3 training stages)
- Road Warrior (2,000+ miles + 90 safety score)
- Master Trainer (200 skill sign-offs given to other movers)
- Piano Man (10 pianos moved damage-free)
- Branch MVP (win quarterly MVP award)

### Future Engagement Ideas (V2+)

- **Daily Quiz:** 60-second quiz from employee handbook + standards of moving manual. Content source: physical training booklet + operational policies. Creates a quiz completion streak.
- **Praise Submission Streaks:** Submit peer praises consistently → keeps recognition culture active.
- **Einstein Mascot:** Use the Einstein head icon at milestone celebrations, empty states, and onboarding. Joy-sparking, not silly. Think Geico Gecko energy — recognizable brand character at key moments.
- **Duolingo-style Streak Calendar:** Week strip showing active days with color-coded dots. Already prototyped on Pulse screen.
- **Share Cards:** Auto-generate shareable milestone cards (Instagram/social format) when streaks hit milestones. V2 feature, not MVP.

### Behavior Design Principle

The app should encourage the right behaviors **without creating perverse incentives.** Examples:
- Shift Streak rewards showing up → good. But shouldn't penalize legitimate PTO.
- Damage-Free Streak rewards care → good. But shouldn't discourage damage reporting.
- Quiz Streak rewards learning → good. Low-stakes, no negative consequences for missing.
- All notifications remain positive-only. Broken streaks are visible in-app but never pushed.

**Open question:** How do we handle legitimate time off (PTO, sick days) without breaking streaks? Options: "streak freeze" (like Duolingo), PTO days don't count against streak, or separate "consecutive work days" from "days without unexcused absence."

---

## Quarterly Theme Integration (Added 3/22)

The current quarterly theme and its critical numbers should be **ambient throughout the app**, not buried in Branch Home.

**Where the theme appears:**
1. **Pulse (home):** Dark banner at top — theme name, mover's personal critical numbers (e.g., safety score, at-risk status, claims count)
2. **My Stats:** Expanded theme section with personal + branch comparison numbers
3. **Branch Home:** Full theme section with branch-level metrics, announcements, and context

**Theme content rotates quarterly:**
- Q1 2026 (Driver's Ed): Safety score + at-risk driver status + claims count
- Future themes might surface: DPH, review scores, quote accuracy, training completion — whatever the critical numbers are.

**Design:** Theme banner is a reusable component. Just swap the metrics and copy each quarter.

---

## Digital Training Guide (Added 3/22)

The physical "Einstein Training Guide" booklet has been mapped into the Level Up screen.

### Physical Booklet → Digital Translation

| Physical | Digital |
|----------|---------|
| Paper booklet on truck dash | Training Guide tab in Level Up screen |
| Lead signs name + date on paper | Lead enters PIN on mover's phone, OR lead scans QR code |
| Manager reviews between stages (in-person form) | Digital gate — manager taps approval after reviewing ratings |
| "Leave booklet in office" recommendation | Always accessible on phone — instructions, videos, photos always available even after completion |
| Max 3 signatures per day rule | TBD — keep as digital rule, or remove constraint? |

### Three Stages (from booklet)

**Stage 1: The Basics** (5-10 workdays, 7 skills)
- Administrative (clock in/out, hours, schedule)
- Backup Signals (ground guide your driver)
- Truck Checks
- Morning Truck Duties (alerts, equipment, pads)
- Protection (doors, floor runners)
- Ramp Etiquette (loading, unloading, pin)
- Clean Up & Resecure Equipment

**Stage 2: Co-Pilot Skills** (15-20 workdays, 22 skills + 10 driving hours)
- Co-Pilot Responsibilities (handle calls, check in with mgmt)
- Wrapping/Loading: dresser, nightstand, coffee/side table, chair (hog tie), shrink wrap, lamp/fragile, TV, dining table, couch, truck loading, washer/dryer, fridge, bookcase/entertainment center, gondola, stacking boxes (15 skills)
- Disassembly/Reassembly: bed, table, mirror/dresser, desk (4 skills)
- Packing: dishes, fragile items (2 skills)
- Driving Hours: 10 hours required, one section per day, only actual drive time logged

**Stage 3: Lead Skills** (5-10 workdays, 8 skills)
- Fill out a move contract
- Explain non-moveable items
- Conduct a walk-through (point out damages, call out waivers)
- Provide an accurate estimate
- Walk a client through the contract
- Handle a damage (send "Training Damage" email if no damages occur)
- Explain a liability waiver to a customer
- Disconnect/connect washer & dryer (check safety bolts on front-load)

**Between each stage:** Manager Status Review — ratings on Attitude, Punctuality, Communication, Service, Teamwork. Must pass to proceed.

### Skill Detail View (prototyped)

Each skill expands to show:
1. **Training video** — embedded video player (where Mike's video content + Austin L's productions live)
2. **Written instructions** — step-by-step text with specific details (always accessible, even after completion)
3. **Photo proof** — optional/required camera capture (Mike's blueprint requires for certain skills)
4. **Sign-off history** — who signed, when (each skill needs 2 sign-offs)
5. **Get sign-off** — lead confirmation flow

### Sign-Off Mechanism (Decision Needed)

| Option | How It Works | Pros | Cons |
|--------|-------------|------|------|
| **Lead enters PIN** | Hand phone to lead, they type 4-digit PIN | Simple, works offline, no lead phone needed | Sharing PINs, social pressure to just sign |
| **Lead scans QR** | Mover shows QR, lead scans with their phone | Proves proximity, lead uses own device | Requires lead to have app access |
| **Lead taps on their phone** | Notification sent to lead's app, they approve | Best audit trail, async possible | Lead needs app, adds friction |

### How This Relates to Mike's 8-Sprint Blueprint

Mike's Digital Training Blueprint is the *enhanced accountability layer* on top of this same content:
- Mike's sprints roughly map to the booklet's stages but break them into smaller units
- Mike adds: video verification (95% watch threshold, can't skip), photo proof requirements, digital praise submissions (ECP structure), eNPS surveys at sprints 3 and 8
- Mike's locked sprint progression = the booklet's manager gate between stages
- Mike's blueprint should inform the V2 of this training system — the booklet content is the foundation, Mike's accountability framework layers on top

### Future: Employee Handbook + Standards of Moving as Content

Cameron's insight: the full employee handbook and "how to move" standards manual could be loaded as content for:
- **Daily quizzes** — 60-second knowledge checks pulled from handbook content
- **Reference library** — searchable, always accessible from Level Up
- **Onboarding content** — new hires read through policies digitally instead of paper orientation packet

This is V2+ but the architecture should assume this content library exists eventually.

---

## Decisions Needed

| # | Decision | Who | Impact |
|---|----------|-----|--------|
| 1 | **Bonus formula** | Cameron + Paul | Blocks the Simulator — the most differentiated feature |
| 2 | **Call-in point window** | Cameron + Anne | Blocks Attendance Planner |
| 3 | **Build approach** | Cameron + Matisen | Fork Sergio? Rebuild? Cameron prototype? |
| 4 | **Comp visibility** | Cameron + Paul | Sergio shows comp openly — is that policy company-wide? |
| 5 | **Sergio's role** | Cameron + Mike | He built the best mover tool at Einstein. Leverage him. |
| 6 | **Timeline** | Matisen | Q2 or Q3? 2-person dev team capacity. |
| 7 | **Pilot branch** | Cameron | Houston (already familiar) or another? |
| 8 | **Mike's training: beta timeline** | Mike + Matisen | When does Copilot Training go digital? |
| 9 | **Sign-off mechanism** | Cameron + Mike + Matisen | PIN vs QR vs lead's phone for training skill sign-offs |
| 10 | **Photo proof: required or optional?** | Cameron + Mike | Per-skill or universal? Mike's blueprint says required. |
| 11 | **Video before sign-off?** | Cameron + Mike | 95% watch threshold (Mike) or optional reference? |
| 12 | **3-signature daily limit** | Cameron + Mike | Keep as digital rule or remove physical constraint? |
| 13 | **Streak freeze for PTO** | Cameron + Anne | How to handle legitimate time off without breaking streaks |
| 14 | **Employee handbook digitization** | Cameron + Mike + Anne | Scope, timeline, who owns content curation for daily quizzes |

---

## Success Metrics

| Metric | Target (6 mo post-launch) | How to Measure |
|--------|---------------------------|----------------|
| Daily active users | 40% of movers (~120/300) | Auth logs |
| "How many points?" questions | 80% reduction | BM survey |
| Training completion rate | 2x baseline | SMART Driving + training guide data |
| Manager time saved | 2-3 hrs/week per BM | BM survey |
| Data freshness | <24 hours (vs. 2-month lag) | EMMA cron cycle |
| Branch adoption | All 10 branches within 3 months | Deployment tracking |
| Avg shift streak | 14+ days | App data |
| Badge completion rate | 50% of movers earn 5+ badges in first quarter | App data |
| Quiz engagement | 30% daily participation (if built) | App data |
| Training guide completion time | 20% faster than physical booklet | Comparison study |

---

## Design Source Files

| Document | Location | What It Contains |
|----------|----------|-----------------|
| **This spec (V3.1)** | `Supporting Projects/Matisen - IT/Mover Super App/mover-super-app-spec.md` | Merged vision + features + gamification + training guide + architecture |
| **Interactive prototype** | `Supporting Projects/Matisen - IT/Mover Super App/prototype.html` | All 8 screens, working sliders/gauges/streaks/badges, skill detail view with sign-off flow |
| **Clean-sheet design** | `Supporting Projects/Matisen - IT/Mover Super App/clean-sheet-design.md` | First-principles design with detailed screen descriptions, interaction patterns, emotional design rationale |
| **Mike's training blueprint** | `~/Downloads/EMC_Digital_Training_Blueprint (Leadership Review).docx` | Full Copilot + Lead Training spec with video verification, photo proof, praise requirements, manager dashboard |
| **Physical training booklet** | `~/Downloads/Einstein Training Booklet.pdf` | 3-stage training guide (The Basics, Co-Pilot Skills, Lead Skills). 37 skills, manager gates, driving hours log. Source content for digital training guide. |
| **Sergio's app (live)** | `einstein-movers-performance-dashboard-74375884848.us-west1.run.app` | 19-tab reference implementation for Houston |
| **System Architecture Playbook** | `Supporting Projects/Matisen - IT/System Architecture/` | V2 playbook with 8-phase roadmap. Mover App = Phase 8 but can launch independently. |

---

## Architecture Context & System Architecture Overlap

The Mover Super App is the **user-facing surface** of the broader system architecture vision. Here's how they connect:

| System Architecture Phase | Mover App Dependency |
|--------------------------|---------------------|
| Phase 0: Security fixes | Should happen first, not a hard blocker |
| Phase 1: Data integrity (Customer/Move schema) | Not directly — Mover App uses employee/games/attendance, not move schema |
| Phase 2: Integration resilience | Nice-to-have for data freshness (cron reliability) |
| **Phase 3: Shared services** | **YES for V2** — connects Replit islands (SMART Driving, Eval Tracker). MVP launches without it via API bridges. |
| Phase 4: Reporting/BI | Powers branch dashboards (Layer 2 of 4-layer vision) |
| Phase 5-7 | Not blockers for mover-facing features |

**The 4-Layer Platform Vision:**
1. **Mover Super App** (this spec) — individual mover dashboard, gamification, training
2. **Branch Dashboards** — Sergio's branch view scaled company-wide, manager tools
3. **Leadership Dashboards** — Roundtable-level KPIs, cross-branch comparison
4. **Central Data Bus** — unified data layer connecting EMMA, Replit apps, Samsara, external sources

**Key insight:** Sergio proved you can build a great mover app on top of a spreadsheet. With EMMA's live API underneath, it becomes the real thing. The Mover Super App is Layer 1, and it can launch before Layers 2-4 are complete. Each layer builds on the data infrastructure of the one below it.

**System Architecture Playbook V2 lives at:** `Supporting Projects/Matisen - IT/System Architecture/`
The playbook covers the full technical roadmap (39-item wish list, AI readiness framework, 8-phase plan, 7 pending decisions) that the Mover Super App will eventually benefit from but doesn't block on.
