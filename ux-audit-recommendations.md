# Mover Super App — UX Audit & Recommendations

**Date:** March 22, 2026
**Version audited:** Prototype V3.1 (prototype.html)
**Framework:** UX best practices, behavior design (Fogg, Hook Model, SDT), gamification research (Duolingo, Strava, Noom, NRC), field-worker mobile patterns, accessibility standards

---

## What the Prototype Gets Right (Keep These)

Before the improvement list — what's already working well:

1. **"Color IS the information"** — Green/orange/red stat tiles communicate status in 2 seconds. This is the single best design decision in the app.
2. **Performance first, money second** — The Pulse leads with stats, bonus is below. This was Cameron's call and it's correct per SDT (competence > external reward).
3. **Positive-only notification strategy** — Aligns with behavior science. Negative pushes create app avoidance.
4. **Bottom nav, phone-first** — Thumb zone compliance is good.
5. **The Simulator is genuinely differentiated** — No other mover app lets you model "what happens if I..." This is the feature Matisen should pitch.
6. **Streak hierarchy (1 primary / 4 active / unlimited badges)** — Correct gamification architecture. One flame, not twenty.
7. **Daily Quiz sourced from real handbook content** — Engagement + training reinforcement in one feature. Wrong answers don't break streak = smart.
8. **Ask Einstein** — An AI assistant backed by actual company policy is a genuine competitive advantage.

---

## Recommendations — Prioritized

### Tier 1: Must-Have (Build These Before Showing to Team)

---

#### 1. ONBOARDING FLOW (First-Time User Experience)

**Problem:** Right now the app opens directly to Marcus's Pulse with no context. A new mover would be lost. There's no "how did I get here" or "what am I looking at."

**Recommendation:** 3-screen onboarding:

**Screen 1 — Welcome + Identity (< 15 seconds)**
- "Welcome to Einstein" + mascot
- Select your branch (dropdown)
- Find your name (searchable list — same as Sergio's current model)
- [Let's Go] button

**Screen 2 — Your Pulse with Guided Tooltips**
- Show their real Pulse screen, but overlay 3 orange tooltip bubbles:
  - On stat tiles: "These are your key numbers. Green = crushing it."
  - On streak flame: "This counts your consecutive shifts. Keep it burning."
  - On bonus card: "Tap here to simulate your bonus."
- Each tooltip has "Got it" dismiss button. No "Next/Back" carousel.

**Screen 3 — There is no Screen 3.** They're already using the app.

**Why:** Duolingo's onboarding gets users to value within 30 seconds. Noom's onboarding investment quiz increases retention 2x. Your onboarding should be the fastest in the industry — movers have zero patience for setup flows.

**Priority:** CRITICAL — without this, the prototype demo falls flat when someone other than Cameron sees it.

---

#### 2. SPANISH LANGUAGE TOGGLE

**Problem:** A significant portion of your 300+ movers speak Spanish as a primary language. The app is English-only.

**Recommendation:**
- Language toggle on the welcome screen (two buttons: English / Español) — not buried in settings
- Store preference in localStorage, never ask again
- Translate UI labels only — proper nouns (Einstein Games, SMART Driving) stay English
- Numbers, currency, and star ratings are universal — stat tiles are already language-light
- Have a bilingual team member review all Spanish copy (don't machine-translate)

**Implementation:** Simple i18n object. Every text label gets a `t('key')` function call. No framework needed.

**Why:** This isn't a nice-to-have. It's the difference between 60% adoption and 90% adoption.

**Priority:** CRITICAL for launch. Can prototype with English-only but must ship bilingual.

---

#### 3. TEXT STATUS LABELS ON STAT TILES (Accessibility + Sunlight)

**Problem:** Stat tiles rely on color alone (green/orange/red) to communicate status. In direct sunlight, colors wash out. For the ~8% of male movers who are color blind (~24 people), green and red look similar.

**Recommendation:** Add a small text label below each stat value:
- Green tiles: "Good" (or a checkmark icon)
- Orange tiles: "Watch" (or a warning icon)
- Red tiles: "Act Now" (or an alert icon)

Color remains the primary signal but text/icons serve as backup.

**Why:** Moving happens outside. Movers check their phones between houses, on truck dashes, in parking lots. If you can't read the color, the tile loses its meaning.

**Priority:** HIGH — easy to implement, big accessibility impact.

---

#### 4. ESCALATING STREAK FLAME ANIMATION

**Problem:** The streak flame looks the same at day 3 and day 90. There's no visual reward for maintaining a long streak.

**Recommendation:** Flame evolves based on streak length:
- Days 1-7: Small flicker (current)
- Days 8-14: Steady flame (slightly larger)
- Days 15-30: Roaring flame (more movement, slight glow)
- Days 31-60: Golden flame (color shifts to gold)
- Days 61-90: Blue flame (rare, prestigious)
- Days 91+: Purple/diamond flame (legendary)

**Why:** Forest app and Duolingo both use escalating visual rewards. The flame becomes something you don't want to lose — not just because of the number, but because of how it looks. "I've got a blue flame" becomes a status symbol.

**Priority:** HIGH — simple CSS animation changes, massive engagement impact.

---

#### 5. ONE-TAP "PROPS" BUTTON

**Problem:** Peer recognition currently requires navigating to the Feed and submitting a structured praise. That's too much friction for casual acknowledgment.

**Recommendation:** Add a "Props" button (👊 fist bump) on:
- Every mover's row in the leaderboard
- Every review/praise/milestone in the Feed
- Every crew member's avatar on the Schedule screen

Single tap = props given. No text required. Props count shows on the mover's profile. "Marcus: 47 props this quarter."

**Why:** Strava's "Kudos" button is their #1 engagement feature. It turns passive viewing into active participation. It takes zero effort but creates social reciprocity ("they gave me props, I should give some back").

**Priority:** HIGH — tiny feature, outsized engagement impact.

---

### Tier 2: Should-Have (Build Before Pilot Launch)

---

#### 6. PROGRESSIVE NAV UNLOCK (14-Day Onboarding Ramp)

**Problem:** All 8 screens are available immediately. New movers are overwhelmed.

**Recommendation:** Unlock screens over the mover's first 14 days:

| Day | What Unlocks | How |
|-----|-------------|-----|
| Day 1 | Pulse + Schedule | Only 2 nav icons shown. Others hidden. |
| Day 3 | My Stats | Nav icon appears with "New!" badge |
| Day 5 | The Board | Push: "You're ranked #X. See where you stand." |
| Day 7 | Simulator | Push: "What if you could see your bonus before it's earned?" |
| Day 14 | Level Up + Feed + Branch Home | Full nav revealed |

**Why:** This is the Noom playbook — don't show everything on Day 1. Each unlock feels like a reward. By Day 14, the mover has built the daily habit before seeing all the features.

**Priority:** MEDIUM-HIGH — important for retention but not blocking for initial demo.

---

#### 7. CREW CHALLENGES

**Problem:** The leaderboard is individual-only. Movers work in crews. There's no team-based competition.

**Recommendation:**
- **"Crew of the Week"** — every move generates a crew score (on-time + damage-free + review + quote accuracy). Weekly leaderboard ranks crews.
- **"Perfect Move" badge** — all crew metrics green on a single move. Rare badge, awarded to all crew members.
- Crew pairings visible: "You've worked with Diego 12 times. Crew avg: 4.9★" — makes high-performing partnerships visible.

**Why:** Moving is team work. Individual competition alone can create toxic dynamics ("I don't want to work with the new guy — he'll hurt my score"). Crew challenges turn it into "I want to help the new guy because we win together."

**Priority:** MEDIUM-HIGH — builds the right culture.

---

#### 8. EMPTY STATES WITH WIFM COPY

**Problem:** When a section has no data (new hire, no reviews yet, no badges), it just shows nothing or placeholder data.

**Recommendation:** Every empty state should answer three questions:
1. What goes here?
2. How do I get it?
3. Why should I care?

**Examples:**
- No reviews yet: "No reviews yet — but your first one could drop any day. When a customer mentions you by name, it shows up here. Movers with 5+ reviews this quarter average $45 more in bonus."
- No badges: "Your badge wall is empty — for now. Complete your first 7-day streak to earn 'On Fire.' You're 7 shifts away."
- No praises: "Praises from your teammates and managers will show up here. Want to kick it off? Give someone props."

**Why:** Empty states are where new users decide "this isn't for me" or "I want to fill this up." The WIFM (What's In It For Me) framing converts empty into aspirational.

**Priority:** MEDIUM — matters most for new hires during onboarding period.

---

#### 9. STREAK FREEZE / PTO HANDLING

**Problem:** The streak system will immediately create frustration if a mover takes a legitimate day off and their streak breaks.

**Recommendation:**
- **Auto-freeze on scheduled days off.** Not scheduled = streak isn't counting. Only scheduled shifts count.
- **1 free "streak save" per month** for unscheduled absences. Fires automatically. "Your streak was saved! 1/1 used this month."
- **Approved PTO pauses the streak**, doesn't break it. "Welcome back! Your 22-day streak is waiting."
- **No-call/no-show breaks the streak.** This is fair and everyone knows it.

**Why:** Duolingo's #1 user complaint is losing streaks on vacation. Solve this before launch or the streak system will generate resentment instead of motivation.

**Priority:** MEDIUM-HIGH — not needed for prototype demo but must be designed before pilot.

---

#### 10. WELCOME BACK SCREEN (Lapsed User Recovery)

**Problem:** If a mover doesn't open the app for 30+ days, they come back to a screen full of stale data and no context.

**Recommendation:** After 30+ days of inactivity, show a catch-up screen:

"Hey Marcus, welcome back. Here's what happened:"
- Your rank: #4 → #9
- 2 new reviews (4.5★ avg)
- 1 point expired (you're at 1.0)
- Projected bonus: $340
- [See My Pulse →]

**Why:** This is a data screen, not a guilt screen. No "We missed you!" cringe. Just information. The mover re-orients in 5 seconds and is caught up.

**Priority:** MEDIUM — not needed for prototype but important for retention after launch.

---

### Tier 3: Nice-to-Have (V2 / Post-Pilot)

---

#### 11. VOICE INPUT FOR TEXT FIELDS

Add a microphone icon next to every text input (praise submissions, Ask Einstein, damage notes). Movers can't type easily with work gloves or sweaty hands. Speech-to-text is built into every modern phone — just expose the `speech` input type or use the Web Speech API.

---

#### 12. OFFLINE DATA CACHING

Cache the Pulse screen data in localStorage on every load. If the mover opens without signal (inside a house, rural area), show last-loaded data with "Last updated: 7:12 AM" timestamp. The app shell (nav, layout, static assets) should work fully offline via service worker.

---

#### 13. PERSONALIZED PULSE SCREEN

Let movers choose which stats appear first on their Pulse. "I care most about reviews" vs. "I care most about DPH." Drag-to-reorder the stat tiles. This satisfies the autonomy need from Self-Determination Theory.

---

#### 14. BRANCH MANAGER DASHBOARD (companion view)

A separate tab/mode for branch managers showing:
- Team overview (all mover stats at a glance)
- Training progress across all movers
- Pending sign-off requests
- Who's at risk (attendance, safety)
- Compare movers side-by-side (already in Sergio's app)

Not part of the mover-facing prototype, but Matisen will ask about this.

---

#### 15. DAILY TIP BETWEEN SCREENS

Insert a 1-sentence tip during screen transitions (like Noom does between food logging): "Did you know? Movers who check the app daily earn 22% higher bonuses." Dismissible with a tap. Rotates from a tip bank. Builds knowledge passively.

---

#### 16. "PERSONAL BEST" MARKERS

On every stat, show the mover's all-time best alongside current: "Safety: 87 (PB: 93)". This creates a "beat your own record" motivation that's purely intrinsic — you're competing with yourself, not others.

---

#### 17. SHARE CARDS (Social Media)

At milestone celebrations, auto-generate a branded Einstein card (Instagram-friendly square format) with the mover's name, achievement, and Einstein branding. One-tap save to camera roll. "I just hit a 30-day shift streak at Einstein Moving!" Free employer brand marketing.

---

#### 18. SEASONAL/LIMITED BADGES

Time-limited badges that drive urgency: "Summer Soldier" (zero call-outs June-August), "Holiday Hero" (worked every DNR day in Q4), "Training Day MVP" (best training day scores). These create FOMO and seasonal engagement spikes.

---

#### 19. ASK EINSTEIN — LEARNING FROM QUESTIONS

Log every question asked to Ask Einstein. Monthly report to Mike/Cameron showing top 20 questions. This is a diagnostic goldmine — if 40 movers ask "how do I handle a damage?" that's a training gap. The AI becomes a teacher AND a sensor.

---

#### 20. DAILY QUIZ — ADAPTIVE DIFFICULTY

Track which quiz categories each mover gets wrong most. Serve more questions from weak areas. If Marcus always misses appliance questions, he gets more appliance questions. Spaced repetition (like Anki) for operational knowledge.

---

## Summary: Build Order

| Priority | Item | Effort | Impact |
|----------|------|--------|--------|
| **Now** | Onboarding flow (3 screens) | Medium | Critical |
| **Now** | Text status labels on tiles | Low | High |
| **Now** | Escalating streak flame | Low | High |
| **Now** | One-tap Props button | Low | High |
| **Before pilot** | Spanish language toggle | Medium | Critical |
| **Before pilot** | Progressive nav unlock | Medium | High |
| **Before pilot** | Empty states with WIFM copy | Low | Medium |
| **Before pilot** | Streak freeze / PTO handling | Medium | High |
| **Before pilot** | Crew challenges | Medium | High |
| **Before pilot** | Welcome back screen | Low | Medium |
| **V2** | Voice input | Low | Medium |
| **V2** | Offline caching | Medium | Medium |
| **V2** | Personalized Pulse | Low | Medium |
| **V2** | Branch manager dashboard | High | High |
| **V2** | Daily tips | Low | Low |
| **V2** | Personal best markers | Low | Medium |
| **V2** | Share cards | Medium | Medium |
| **V2** | Seasonal badges | Low | Medium |
| **V2** | Ask Einstein analytics | Medium | High |
| **V2** | Adaptive quiz difficulty | Medium | Medium |

---

## Behavior Design Cheat Sheet

**BJ Fogg (B = MAP):** For each behavior, ensure Motivation + Ability + Prompt all exist. If movers aren't checking the app → either the motivation is weak (show them why it matters), the ability is hard (reduce taps), or the prompt is missing (push notification).

**Hook Model:** Trigger (push notification with data) → Action (open app, glance at Pulse) → Variable Reward (rank changed? new review? bonus moved?) → Investment (streak grows, badges accumulate, training progresses). The variable reward is key — the Pulse must change daily.

**Self-Determination Theory:**
- Autonomy: "Your stats, your choice, your simulator." Never surveillance language.
- Competence: Performance rings fill, badges accumulate, coaching tips show specific next steps.
- Relatedness: Crew challenges, peer props, branch pride, shared streaks.

**Golden rule:** Lead with intrinsic motivation (competence, mastery, pride), support with extrinsic (money, rank, badges). The Pulse does this correctly — stats first, bonus second.
