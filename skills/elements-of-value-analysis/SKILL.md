---
name: elements-of-value-analysis
version: 2.0.0
description: >
  Apply the Bain/Almquist Elements of Value framework in two modes. Mode A — Inventory
  Mapping: map existing products, services, agents, or tools against all 30 elements to
  find value drivers, cross-tier stacking, and proof points for a pitch or deck. Mode
  B — Opportunity Lens: forward-looking evaluation of any opportunity (agent idea,
  product, consulting offer, job-search bet) answering what to build, how to sell it,
  and what the end goal really is, with a stack score and a proceed/reshape/park
  verdict. Trigger Mode A on: "run an elements of value analysis," "EoV analysis,"
  "value stack analysis," "map these to the Almquist pyramid." Trigger Mode B on:
  "run the opportunity lens," "EoV lens on X," "is this worth building," "will this
  actually deliver value," or when Jeff proposes a new build and wants assurance it
  delivers real value. Do NOT trigger for general value discussion, positioning copy,
  or StoryBrand messaging.
---

# Elements of Value Analysis

Two modes, one framework. Both use the same 30 elements, the same binary scoring
discipline, and the same calibration anchor. They differ in direction and output:

| | Mode A — Inventory Mapping | Mode B — Opportunity Lens |
|---|---|---|
| Direction | Backward-looking: what value do things I've built deliver? | Forward-looking: will this thing deliver value if I build/sell/pursue it? |
| Input | A set of existing items (2+) | One or more candidate opportunities |
| Output | Visual matrix + stacking analysis + deck framing | One-page lens per opportunity: checklist, stack score, Build/Sell/Goal row, verdict |
| Use when | Preparing a pitch, deck, or proof-point narrative | Deciding whether to build, what to cut, how to position, or whether to kill |

**Mode selection:** If the items already exist and the goal is describing their value →
Mode A. If the question is whether/what to build, sell, or pursue → Mode B. If unclear,
ask one question: "Are we describing what you've built, or deciding what to build?"

---

## Shared Framework

### The 30 Elements of Value

The pyramid has four tiers. Hitting elements at higher tiers on top of Functional
hits is "stacking" — the hallmark of transformative offerings.

**Tier 1 — Functional (14):** what it operationally does
1. Saves time · 2. Simplifies · 3. Makes money · 4. Reduces risk · 5. Organizes ·
6. Integrates · 7. Connects · 8. Reduces effort · 9. Avoids hassles · 10. Reduces cost ·
11. Quality · 12. Variety · 13. Sensory appeal · 14. Informs

**Tier 2 — Emotional (10):** how it makes the user feel
15. Reduces anxiety · 16. Rewards me · 17. Nostalgia · 18. Design/aesthetics ·
19. Badge value · 20. Wellness · 21. Therapeutic value · 22. Fun/entertainment ·
23. Attractiveness · 24. Provides access

**Tier 3 — Life Changing (5):** impact on life trajectory
25. Motivation · 26. Provides hope · 27. Self-actualization · 28. Heirloom ·
29. Affiliation/belonging

**Tier 4 — Social Impact (1):** contribution beyond the individual
30. Self-transcendence

**B2C vs. B2B note:** This is the 30-element B2C pyramid. For B2B offerings the
stacking logic still holds as an approximation, but Bain's separate 40-element B2B
pyramid covers additional dimensions ("Reduced risk," "Ease of doing business") that
map differently. State this assumption in the output when items are B2B.

### Scoring Rules (both modes)

Score each element as a binary hit or miss.

**Hit when:**
- The element is a genuine, direct value delivered — not a stretch
- It would plausibly appear in the top 5 reasons someone uses or chooses this
- The benefit is reliable and consistent, not occasional or edge-case

**Miss when:**
- The element applies only tangentially or as a downstream effect
- You need 2+ reasoning steps to get there ("saves time → earns money → reduces
  stress": credit only "saves time")
- You're uncertain — err toward no. Inflated scores obscure what differentiates
- The benefit applies only to an edge-case user

**Tier-specific notes:**
- Sensory appeal, Nostalgia, Therapeutic value, Fun/entertainment: most digital and
  operational tools score zero here. That's correct — don't force hits.
- Self-transcendence: requires enabling contribution genuinely beyond the user's
  self-interest. Rare and meaningful when it applies.
- Heirloom: only when the product builds lasting value for future people (family
  wealth, legacy, durable assets).
- Provides access: opens a door previously unavailable — a platform, audience,
  opportunity — not just "it helps."

**Calibration anchor — Todoist (task manager):**
- Functional 7/14: Saves time, Simplifies, Organizes, Reduces effort, Avoids
  hassles, Quality, Informs
- Emotional 1/10: Reduces anxiety
- Life Changing 1/5: Motivation
- Total: 9/30

Todoist does NOT hit "Makes money" (2-step chain), "Integrates" absent evidence of
active cross-tool workflows, "Badge value," or "Provides hope." Use this as the
calibration floor for similar tools.

### Scores (both modes)

For each item/opportunity compute:
- **Tier tallies:** Functional x/14 · Emotional x/10 · Life Changing x/5 · Social Impact x/1 · Total x/30
- **Stack Depth (0–4):** number of tiers with ≥1 hit
- **Stack Score:** weighted total = (Functional hits × 1) + (Emotional × 2) +
  (Life Changing × 3) + (Social Impact × 4). Max 53. The weighting encodes Bain's
  finding that higher-tier elements drive loyalty and willingness-to-pay
  disproportionately. [Inference] The specific weights are a working heuristic, not
  Bain's published methodology — treat the score as a comparison device between
  opportunities, not an absolute truth.

A **cross-tier stacker** hits Functional + Emotional + Life Changing simultaneously.
Social Impact is tracked and noted but doesn't affect the stacker designation.

---

## Mode A — Inventory Mapping

### Process
1. **Intake.** Identify the items (extract from context or ask). One-sentence
   description each.
2. **Score** all 30 elements per item, tier by tier. Borderline test: "Would a
   typical user cite this in their top 5 reasons?"
3. **Tally** tier scores, Stack Depth, Stack Score.
4. **Identify cross-tier stackers** — the strongest proof points; flag prominently.
5. **Render** the visual matrix with show_widget (see Output Format).
6. **Insights** — 3-5 sentences on positioning, not score restatement.

### Output Format
- **Summary cards** (top): item name, total/30 (large), tier chips "F x/14" (blue),
  "E x/10" (coral), "LC x/5" (green), "SI x/1" (purple), "cross-tier stacker" badge +
  green border for F+E+LC items.
- **Legend:** four tier dots + dash for misses.
- **Matrix table:** items as columns, elements as rows (≤6 items); transpose for 7+.
  Tier header rows group elements. Color-coded dots per tier, dashes for misses,
  table-layout: fixed.
- **Insight box** (bottom): stacking story, tier-distribution story (10 functional +
  0 emotional = workflow automation, not life improvement), niche-but-powerful
  elements (Heirloom, Provides hope, Self-transcendence, Self-actualization), gaps
  no item hits, B2B flag if applicable, and a one-sentence deck framing
  recommendation (which items lead, which support).

### Slide/Deck Framing
- Lead with cross-tier stackers — 2-3 max. These are the anchors.
- Frame stacking explicitly: "This isn't a productivity tool — it delivers value at
  three levels simultaneously" is a slide claim.
- Pure functional items belong in supporting evidence, not the lead slide.
- High emotional/badge-value items → frame around who the user becomes.
- Tier labels as sub-headers read as a natural narrative arc.

---

## Mode B — Opportunity Lens

The purpose of this mode is to prevent building things that don't deliver value.
It is a gate, not a celebration. A run that never returns RESHAPE or PARK is
miscalibrated.

### Process

**Step 1 — Frame the opportunity.**
Capture four facts before scoring anything. If any is missing, ask — do not infer:
- **What it is** (one sentence, concrete)
- **Who the beneficiary is** — the specific person/buyer whose life improves.
  "Me" is a legitimate answer, but say so explicitly; self-serving builds get
  scored on value to Jeff, not to an imagined market.
- **The 90-day version** — score the thing as it will realistically exist in 90
  days, NOT the long-term vision. Vision-scoring is the primary source of inflated
  lenses.
- **Cost of pursuit** — rough effort (hours/weeks) and opportunity cost (what it
  displaces).

**Step 2 — Question 1: WHAT TO BUILD.**
Score all 30 elements (shared rules above) for the 90-day version, from the
beneficiary's seat. Compute tier tallies, Stack Depth, Stack Score. Then answer:
which scored elements are load-bearing (the build fails if they're not delivered),
and which are incidental? The load-bearing elements define the minimum build —
anything in the plan that doesn't serve a load-bearing element is a cut candidate.

**Step 3 — Question 2: HOW TO SELL IT.**
Take the 2-3 strongest scored elements (prefer highest tier with a solid functional
base) and convert each into a positioning line — the message a buyer would hear.
Check against Jeff's brand architecture (systems builder, AI-native operations,
financial rigor): does the positioning reinforce the brand or dilute it? If the
opportunity is externally sold, note the handoff: run `storybrand` for the full
message architecture. If the beneficiary is "me," this question becomes "how does
this become a proof point?" (deck, blog post, interview story) — an opportunity
with no sellable story and no proof-point value must justify itself on functional
value alone.

**Step 4 — Question 3: WHAT THE END GOAL REALLY IS.**
Name, in one sentence, the outcome the stacked value serves — the thing that is
true in the world when this succeeds. This ties to ODF/define-done: if an
outcome_charter exists, check consistency; if the end goal can't be stated in one
sentence, that is an automatic RESHAPE flag — the opportunity is a solution
looking for a problem.

**Step 5 — Kill questions.**
Answer each honestly before rendering a verdict:
1. Whose emotional hits are these — the user's, or mine as the builder? (Building
   is fun; that's not user value.)
2. Would the beneficiary pay — money, time, or attention — for the 90-day version?
3. Does the cost of pursuit exceed the functional value delivered?
4. Is there an existing tool that delivers 80% of this stack for ~zero build cost?
5. What has to be true for the Life Changing hits to be real? (These are the most
   commonly inflated.)

**Step 6 — Verdict.**
Render one of three, with one sentence of reasoning:
- **PROCEED** — Stack Depth ≥ 3 with ≥5 functional hits, load-bearing elements are
  deliverable in the 90-day version, kill questions pass. Or: Depth ≤ 2 but cost is
  trivial relative to functional value.
- **RESHAPE** — real value exists but the current spec doesn't deliver it: e.g.,
  emotional/LC hits with a weak functional base (story without substance), scope
  that serves no load-bearing element, no statable end goal, or positioning that
  fights the brand. Name the reshape in one sentence.
- **PARK** — kill questions fail, cost exceeds value, or an existing tool covers
  the stack. Parking is a success of the lens, not a failure of the idea.

These thresholds are judgment aids, not formulas. [Inference] Override them when
warranted, but state the override and why.

**Step 7 — Render + write back.**
Render the one-page lens (Output Format below). Then offer — do not do without
approval — to write the verdict back: a CoS Decision Log entry (Date / Decision /
Why / Tradeoff / Reverses-if / Horizon) and/or Todoist task updates.

### Output Format (per opportunity)

One page, rendered with show_widget:
- **Header:** opportunity name, one-line description, beneficiary, verdict badge
  (PROCEED green / RESHAPE amber / PARK gray)
- **Score row:** Stack Score /53 (large), Stack Depth /4, tier chips F/E/LC/SI
- **Checklist:** the 30 elements in four tier groups, hits as colored dots
  (tier colors below), misses as dashes; load-bearing elements marked ★
- **Build / Sell / Goal summary rows:**
  - BUILD: the minimum build in one sentence (load-bearing elements only) + cut candidates
  - SELL: the 2-3 positioning lines (or proof-point framing if beneficiary is "me")
  - GOAL: the one-sentence end goal
- **Kill-question flags:** any that failed or were shaky, stated plainly
- **Verdict box:** PROCEED / RESHAPE / PARK + one sentence

For multiple opportunities in one run, render each lens, then a short comparative
insight: rank by Stack Score, name the strongest stack, and state what that implies
for where the next unit of effort goes.

### Anti-Inflation Discipline

This mode fails if it flatters. Enforce:
- Score the 90-day version, never the vision.
- Beneficiary's seat, not builder's seat.
- The calibration anchor applies: a competent task manager scores 9/30. An
  opportunity scoring 20+ /30 should be rare and should survive hostile questioning.
- At least one element per run should provoke a genuine "no, that's a stretch" —
  if every borderline call went to "hit," rescore.

---

## Design Notes for Widgets (both modes)

Use CSS variables for all colors so widgets work in light and dark mode.

Tier colors:
- Functional: blue ramp (#E6F1FB background, #0C447C dots)
- Emotional: coral ramp (#FAECE7 background, #712B13 dots)
- Life Changing: green ramp (#EAF3DE background, #27500A dots)
- Social Impact: purple ramp (#EEEDFE background, #3C3489 dots)
- Stacker border / PROCEED: #3B6D11; badge #EAF3DE background, #27500A text
- RESHAPE: amber (#FBF0DD background, #7A4F0F text)
- PARK: gray (#EEEEEE background, #555 text)

Target container width: 680px. Mode A matrix: ~120px element-name column, remainder
split evenly (~80-95px per item column for ≤6 items). Mode B lens: single-column
one-page layout; checklist as a 2-column grid within each tier group to stay on
one page.
