# Mover Super App — First Principles Design (Clean Sheet)

*Designed without referencing any existing app. Built purely from mover needs + available data.*

---

## The User: A 22-Year-Old Mover Named Marcus

Before designing a single screen, here is the person we are building for.

Marcus is 22. He makes $18.25/hour moving furniture in the Texas heat. His phone is his primary computer. He checks Instagram, plays games, texts his girlfriend, and watches YouTube — all on a 6.1-inch screen with one thumb. He does not read long paragraphs. He processes information through color, size, and pattern. He cares about three things at work: **how much money am I making, am I in trouble, and what's next.**

He does not want a "dashboard." He wants answers to the questions that live in the back of his mind every single shift:

- "What's my bonus looking like this quarter?"
- "How many points do I have? When does my next one fall off?"
- "Where do I rank compared to the other guys?"
- "What am I doing tomorrow?"
- "Can I afford to call in on Friday?"

Every design decision below exists to answer one of those questions — instantly, visually, and without requiring Marcus to interpret a table or read a paragraph.

---

## Core Design Principles

**1. YOUR money, YOUR data.** This is a financial tool disguised as a performance app. The mover's paycheck is the center of gravity. Every metric connects back to money.

**2. Forward, not backward.** Static dashboards show history. This app shows the future. The primary interaction pattern is "what happens IF..." — sliders, simulators, projections.

**3. Color IS the information.** Green means good. Orange means watch it. Red means act now. A mover should be able to glance at this app for 2 seconds and know if their world is fine or if something needs attention.

**4. Yours, not theirs.** Language never sounds like management talking down. No "compliance," no "violations," no "metrics." Instead: "your streak," "your money," "your next level." The app speaks as a personal tool the mover owns — not a surveillance system their boss installed.

**5. Thumb-zone design.** Every primary action is reachable with one thumb on a 6-inch screen. Navigation lives at the bottom. Key interactions are in the center-bottom third of the screen. Nothing critical lives in the top-left corner (the hardest place to reach one-handed).

---

## Identification and Access

**The problem to solve:** Movers should not need to remember a password. They also should not be able to see each other's compensation data. The solution has to be dead simple to get in, but private enough to protect sensitive information.

**How it works:**

- **First launch:** Mover enters their employee ID (printed on their badge) and last 4 digits of their phone number. The app generates a 4-digit PIN they choose. That PIN is stored locally on the device and tied to their EMMA employee record via a lightweight token.
- **Every launch after:** PIN entry only. Four dots, tap four numbers, you are in. Under 3 seconds.
- **Biometric option:** If the phone supports Face ID or fingerprint, the mover can enable it. Then it is zero-tap entry.
- **No email required.** No "forgot password" flow that sends to an email many movers do not check. PIN reset goes through the branch manager, who verifies identity in person (they see each other every day).

**Why this works:** It respects that movers are not office workers. They do not have corporate email. They share phones sometimes. A PIN is something they understand from their banking app. The branch manager reset mirrors how they already solve problems — by talking to their boss in person.

---

## Screen 1: The Pulse (Home Screen)

**Problem it solves:** "What do I need to know RIGHT NOW?"

Every time Marcus opens the app, he should see his world at a glance. Not a wall of data — a pulse check. Three seconds, three signals: money, attendance, rank.

**Layout (top to bottom):**

**Top bar:** "Hey Marcus" with branch name (South Austin) and a small avatar circle with his initials. Current date. No hamburger menu — navigation is at the bottom.

**The Money Card (dominant, top 40% of screen):** A single large card with a dark background (Einstein dark blue). Shows:
- **Projected quarterly bonus** in large type: "$384"
- Label: "Your Q1 bonus, on pace"
- Thin progress bar showing how far through the quarter we are (67% through Q1)
- Delta arrow: "+$22 from last week" in green
- **Tap to open the Bonus Simulator (Screen 3)**

**The Three Signals (middle row, three equal-width tiles):**

| Tile | Shows | Color Logic | Tap Opens |
|------|-------|-------------|-----------|
| **Points** | Circle with "1.5" inside | Green (0-2), Orange (2.5-4), Red (4.5+) | Attendance Planner |
| **Rank** | "#4 of 23" | Neutral | Leaderboard |
| **Streak** | Flame icon + "12 days" | Green (active), Gray (broken) | My Stats |

**Today's Schedule (bottom section):**
- "2 moves today"
- First move: "8:00 AM — 3BR, Leander" with crew size icon
- Second move: "2:00 PM — 1BR, North Austin" with crew size icon
- Tap opens Schedule screen

**Bottom navigation bar (persistent on all screens):**
Five icons: Pulse (home) | Stats | Simulate | Board | More

**The "aha moment":** The money card. A mover opens this app for the first time, sees "$384 — your Q1 bonus, on pace," and immediately understands: this app is about MY money. Not my boss's metrics. MY paycheck.

**What makes it engaging:** The three signals change color every day. That creates a daily micro-check: "Am I still green?" The streak counter creates a game. The money card going up feels like watching a savings account grow.

---

## Screen 2: My Stats (Personal Performance Profile)

**Problem it solves:** "How am I actually doing, and what do I need to work on?"

This replaces the conversation where a mover walks up to their branch manager and says "hey, how am I looking?"

**Layout:**

**Profile header:** Name, role ("Lead Mover"), branch, tenure ("1 yr 4 mo"), hourly rate ($18.25/hr). Compact — one line of key identity info.

**Performance rings (the signature visual):** Four concentric ring segments, like Apple Watch activity rings but with four arcs. Each fills based on performance vs. target:

| Ring | Metric | Green When | Orange When | Red When |
|------|--------|-----------|-------------|----------|
| Outer | Safety (driver score) | 80+ | 60-79 | Below 60 |
| 2nd | Quality (DPH) | Below $1.23 target | At target | Above target |
| 3rd | Service (reviews + quote accuracy) | Above average | Average | Below average |
| Inner | Reliability (attendance) | 0-2 points | 2.5-4 points | 4.5+ points |

Four full green rings = crushing it. A mover can see which ring is lagging without reading a single number.

**Metric detail cards (scrollable below rings):** Each metric gets an expandable card:

- **Safety score:** Current (87), trend arrow, 4-week sparkline, branch rank. Coaching tip: "Your following distance improved. Mobile usage is your next target."
- **Damages:** DPH value, damage-free streak, total $ this quarter. "Goal: under $1.23/hr. You're at $0.45." Green bar with threshold marker.
- **Reviews:** Star average (4.8), count (22), most recent review preview.
- **Quote accuracy:** Reliability %, breakdown (on-track / over / under).
- **Attendance:** Current points, next expiration date, call-in history.

**Coaching section (bottom):** Data-driven, specific coaching tips: "Your DPH has been under $0.50 for 6 straight weeks. If your driver score gets above 85, you'll be in the top 3."

**The "aha moment":** The performance rings. Four green rings = "I'm doing great." One orange ring = "okay, that's what I need to fix." No spreadsheet. No table. Just color and shape.

---

## Screen 3: The Bonus Simulator

**Problem it solves:** "What's my bonus going to be, and what can I do to make it bigger?"

The quarterly bonus can be $200 or $800 depending on performance. Today, movers have no idea what it will be until the check arrives. If a mover can SEE that improving their driver score by 10 points adds $45 to their bonus, they will drive safer.

**Layout:**

**Top: The big number.** "$384" large and bold on a dark card. Label: "Projected Q1 bonus." Subtext: "Based on your current pace + estimated hours."

**The formula breakdown (visual, not textual):** Horizontal stacked bar showing how the bonus is composed:
- Safety score contribution (blue segment)
- Attendance contribution (green segment)
- DPH contribution (orange segment)
- Review/quality contribution (gray segment)

Shows the mover WHERE their bonus comes from — which behaviors are paying off.

**The sliders (the main interaction):** Three interactive sliders, each updating the big bonus number in real time:

| Slider | Range | What It Shows |
|--------|-------|---------------|
| **Driver safety score** | 0-100 | "+$23 if you hit 90" |
| **Projected hours this quarter** | Current → max | "520 hrs = $420. 480 hrs = $365." |
| **DPH (damages per hour)** | Current → zero | "One more $200 damage drops your bonus by $35" |

**Scenario presets (quick-tap buttons):**
- "Best case" — all metrics at personal best. Shows the ceiling.
- "Current pace" — stays where you are. Default.
- "If I slack off" — metrics decline to average. Shows the floor.

**Historical context (collapsed):** "Last quarter you earned $310. You're tracking $74 higher."

**The "aha moment":** Moving a slider and watching the bonus number change in real time. "Oh, if I just get my safety score to 85, I make an extra $40." The bonus stops being a quarterly surprise and becomes a game with visible levers.

**What makes it engaging:** Sliders are inherently playful. People fiddle with them. The "Best case" preset is aspirational. The real-time number change creates a dopamine loop: move slider, see money change, imagine the paycheck. This is the feature that will get movers showing the app to each other.

---

## Screen 4: The Attendance Planner (Call-Out Simulator)

**Problem it solves:** "Can I afford to call in tomorrow? When do my points drop off?"

Call-in points are one of the most stress-inducing parts of a mover's life. Too many points = termination. But they have zero visibility into their balance, expiration dates, or consequences of their next absence.

**Layout:**

**Top: Speedometer gauge.** Large semicircular gauge showing current call-in points. Zones:

| Zone | Range | Label | Color |
|------|-------|-------|-------|
| Clear | 0-2 pts | "You're clear" | Green |
| Watch it | 2.5-4 pts | "Watch it" | Orange |
| Danger | 4.5-5.5 pts | "Danger zone" | Red |
| Terminal | 6+ pts | "Termination risk" | Black |

Needle sits at current balance. Instant visual read.

**Point history timeline (middle):** Horizontal 90-day timeline:
- Gray dots: expired points (no longer counting)
- Orange dots: active points (currently on record)
- Green dot with countdown: next expiration. "0.5 pts drop off in 12 days (April 3)"

Answers the most common question: "When does my next point fall off?"

**The simulator (bottom — the key interaction):** Three buttons:

| Button | Points | Color |
|--------|--------|-------|
| "Late arrival" | +0.5 pts | Orange |
| "Call-out" | +1.0 pts | Darker orange |
| "No-show" | +1.5 pts | Red |

**Tap one → gauge animates to show the NEW balance.** Zone color updates. If it pushes into red/black: "This would put you at 5.0 points. That's the danger zone."

**Date picker:** "What if I call in on [date]?" — because impact differs based on when points expire. Calling in next week might be fine because a point drops off in 3 days.

**"What if I stay clean?" projection:** "If you have zero incidents, you'll be at 1.0 points by April 15." Shows the light at the end of the tunnel.

**The "aha moment":** Tapping "Call-out" and watching the gauge needle swing into the red zone. That visceral moment — "oh, I literally cannot afford to skip tomorrow" — changes behavior more than any written policy. Or the opposite: tapping it and seeing the gauge barely move, confirming "I'm fine, I have room."

---

## Screen 5: The Board (Leaderboard and Games)

**Problem it solves:** "Where do I stack up against my crew?"

**Layout:**

**Top: Your position.** Large card: "#4 of 23" with composite Einstein Games score. "YOU ARE HERE" marker in the list below.

**Leaderboard list (main section):** Scrollable ranked list of all branch movers:
- Rank | Avatar | Name | Score | Trend arrow (up/down/steady)
- Top 3 get medal icons (gold, silver, bronze)
- Your row highlighted with orange left border
- Shows names and scores but NOT compensation (compensation is private)

**Category tabs (horizontal scroll):**
Overall (default) | Safety | DPH | Reviews | Quote Accuracy | Attendance

A mover might be #12 overall but #2 in reviews. These tabs let them find their strengths.

**Branch vs. Branch (bottom card):** "South Austin is ranked #3 of 10 branches." Creates team pride.

**Quarterly MVP spotlight:** Banner celebrating the MVP with standout stats.

**The "aha moment":** Seeing yourself ranked. Whether you are #2 or #15, seeing your name on a leaderboard triggers something primal. The category tabs are the second hit — "I'm #12 overall, but #3 in reviews? I'm actually good at something."

---

## Screen 6: My Schedule

**Problem it solves:** "What am I doing today and this week?"

**Layout:**

**Today view (default):** Vertical timeline of assignments:
- Time: "8:00 AM"
- Move type: "3BR House"
- Location: "Leander" (city, not full address until day-of)
- Crew: Avatar circles with first names. "You + Daniel + Marcus R."
- Truck: "B3-RANGER"
- Duration: "~4 hrs"
- Travel indicator between moves: "45 min drive"

**Week view (tab toggle):** Compressed 5-day view: "Mon: 2 moves (8 hrs) | Tue: 3 moves (10 hrs) | Wed: OFF..."

**Hours tracker (persistent bottom bar):** "This week: 32 hrs. Projected: 42 hrs." And: "This quarter: 410 hrs." Connects directly to Bonus Simulator — more hours = bigger bonus.

**The "aha moment":** Seeing your crew listed before you arrive at the warehouse. "Oh, I'm with Daniel today." Small detail but it makes the mover feel informed and respected.

---

## Screen 7: Level Up (Career and Training)

**Problem it solves:** "What do I need to do to get promoted and make more money?"

Einstein has a defined career ladder with real pay increases at each level. Most movers have no idea what the requirements are, how close they are, or what the next level pays.

**Layout:**

**Career path visualization (top):** Vertical trail with nodes at each level:

| Level | Pay Range | Badge |
|-------|-----------|-------|
| Mover | Starting | (completed) |
| Junior Lead | $18.50/hr + $1.75 bonus | |
| Lead | $19.00/hr + $2.00 bonus | |
| Senior Lead | $19.50/hr + $2.25 bonus | |
| Master Lead | $19.50/hr + $3.00 bonus | |
| Lead General | $19.50/hr + $3.75 bonus | "Genius Status" |

Current position = glowing dot. Next level highlighted with progress bar. Each level shows the pay bump: "+$1.25/hr" in green.

**Requirements for next level (middle):** Checklist card:
- "500 hours logged" — progress bar, 410/500 (82%)
- "Safety score above 80 for 3 consecutive months" — checkmark or progress
- "Complete SMART Driving Program" — 6/8 modules done
- "On-road evaluation passed" — pending or completed
- "Manager recommendation" — binary

**Training modules (bottom, scrollable):** Cards for available training:
- SMART Driving modules (8) with completion %
- Lead Training Program sprints (9-sprint program)
- Title, duration ("~20 min"), completion status, "Start" button

**Badges and achievements:** Collection area:
- "Damage Free 30 Days"
- "5-Star Streak" (3 consecutive)
- "Perfect Attendance" quarterly badge
- "SMART Driver Certified"
- Show up on leaderboard profile too

**The "aha moment":** Seeing the pay bump at each level. "$1.25/hr more" = "$50 more per week. $2,600 more per year." The mover is no longer guessing whether advancement is worth pursuing.

---

## Screen 8: The Feed (Recognition and Social Proof)

**Problem it solves:** "Does anyone notice when I do good work?"

Movers do hard physical labor. On a bad day, they feel like nobody cares. This screen surfaces every piece of positive feedback in a single feed.

**Layout:**

**Unified feed (the entire screen):** Scrollable timeline mixing all recognition types, newest first:
- **Customer reviews:** 5 stars, customer name, review text
- **Peer praises:** Core value tag, who submitted it, message
- **Manager recognition:** Formal recognition with Einstein logo badge
- **Auto-milestones:** "You hit a 30-day damage-free streak!" / "You completed your 100th move!"

**Filters:** All / Reviews / Praises / Milestones

**Share button on each item:** Tap to share a review or praise as a **branded card** (Einstein orange border, mover's name, the quote) to social media or save to camera roll. This is the "show my friends" moment.

**Stats summary (pinned at top):** "22 reviews this quarter (4.8 avg) | 3 praises | 2 milestones."

**The "aha moment":** Reading a 5-star review from a customer who mentioned you by name. Or seeing a praise from a coworker. These moments of recognition, buried in Monday.com today, are now collected and presented personally.

**What makes it engaging:** The shareable card feature. A mover screenshots a branded card with a 5-star review and posts it on Instagram. Free employer branding AND mover pride.

---

## Notification Strategy (Positive Only)

| Notification | When | Why |
|-------------|------|-----|
| Morning schedule | 6:30 AM workdays | "You have 2 moves today. First: 8AM, 3BR Leander." Daily hook. |
| New review | Real-time | "A customer left you a 5-star review!" Dopamine hit. |
| Rank change | Weekly, Monday AM | "You moved up to #3 this week." Weekly narrative. |
| Point expiration | 3 days before | "0.5 points drop off Thursday." Relief notification. |
| Bonus update | Bi-weekly | "Your projected bonus is now $402 — up $18." Money reinforcement. |
| Training nudge | Weekly (if incomplete) | "2 modules from SMART Certified. Next one is 18 min." Gentle, capped. |

**What we do NOT push:** Negative events (damages, safety violations). Those are visible in-app but pushing bad news creates negative association with the app. The app should feel like a reward, not a reprimand.

---

## How This Feels Different From "Management Watching Me"

1. **Data flows TO the mover, not FROM.** The app doesn't collect new data. It takes data management ALREADY has and gives the mover access. "You should see what your boss already sees about you."

2. **First-person language.** "Your bonus." "Your streak." Never "compliance score" or "incident report."

3. **Simulators give POWER, not anxiety.** A surveillance tool says "you have 4.5 points." A personal tool says "you have 4.5 points. One drops off Thursday. If you stay clean until then, you're back to 4.0."

4. **Positive-only notifications.** The app never pushes bad news.

5. **The Feed is 100% recognition.** No "incident log" or "violation history" screen.

6. **No manager view in the mover's app.** The branch manager has their own dashboard (Layer 2). The mover never sees a "manager view" toggle.

---

## The Eight Screens — Summary

| # | Screen | Primary Purpose | Key Interaction | Daily Use |
|---|--------|----------------|-----------------|-----------|
| 1 | **The Pulse** | "What do I need to know now?" | Glance: money, points, rank | Every day |
| 2 | **My Stats** | "How am I doing overall?" | Performance rings — visual health check | 2-3x/week |
| 3 | **The Bonus Simulator** | "What's my paycheck going to be?" | Sliders that change bonus in real time | Weekly |
| 4 | **The Attendance Planner** | "Can I afford to call in?" | Tap absence type, watch gauge respond | As needed |
| 5 | **The Board** | "Where do I rank?" | Leaderboard with category tabs | Weekly |
| 6 | **My Schedule** | "What am I doing today?" | Today's moves + crew + truck | Every workday |
| 7 | **Level Up** | "How do I get promoted?" | Career path + training modules + badges | Weekly |
| 8 | **The Feed** | "Does anyone notice?" | Recognition feed with shareable cards | When notified |

---

## Implementation Phases

**Phase 1 (MVP — 4-5 weeks):** Pulse, My Stats, My Schedule. Existing EMMA data with `/me` endpoints. No new calculation logic.

**Phase 2 (Simulators — 3-4 weeks, parallel):** Bonus Simulator + Attendance Planner. Requires bonus formula (Cameron + Paul) and attendance window confirmation.

**Phase 3 (Social + Career — 2-3 weeks):** The Board, Level Up, The Feed. Engagement and retention features.

**Key dependency:** The bonus formula. Without it, the Bonus Simulator cannot be built. This is a Cameron + Paul decision that should happen before or parallel to Phase 1.
