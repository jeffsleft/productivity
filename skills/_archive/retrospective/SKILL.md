---
name: retrospective
description: Fire when running quarterly portfolio reviews, grading prior theses, or analyzing thesis accuracy against current fundamentals
version: 1.0.0
---

# /retrospective

## Trigger
`/retrospective` — run quarterly (target: first week of each new quarter)

Optionally: `/retrospective TICKER1 TICKER2` to run for specific tickers only.

## What This Skill Does
Quarterly feedback loop. For each ticker that has a prior Analysis Page in Notion, it fetches current price and fundamentals, scores whether the prior thesis held, and appends a timestamped Retrospective section to the Notion page. Produces a portfolio-level accuracy summary at the end: which calls were right, which were wrong, and what patterns emerge in the misses.

The goal is to make conviction scoring self-correcting over time.

---

## Step-by-Step Execution

### Step 1 — Find All Prior Analyses

Search the Analysis Pages DB (data_source_id: `a4f8265c-4e03-4169-af32-8822b24f5cf7`) for all pages.

For each page found:
- Extract the ticker from the page title
- Note the date of the most recent analysis section
- Extract the prior Sizing Signal (Overweight / At-weight / Underweight) from the content
- Extract the prior Personal Thesis (the opinionated takeaway)
- Extract the prior key bull and bear cases

Skip any tickers where the most recent analysis is less than 60 days old — too soon for a meaningful retrospective.

### Step 2 — Fetch Current Data

For each eligible ticker, run:
```
python3 ~/Claude/investing-tool/sa_fetcher.py TICKER [--etf TICKER if applicable]
```

Also run one macro snapshot at the start:
```
python3 ~/Claude/investing-tool/sa_fetcher.py --macro
```

Load updated SA ratings from `~/Claude/investing-tool/sa_ratings.json` (these update when a new Excel export is parsed).

### Step 3 — Score Each Thesis

For each ticker, produce a Retrospective Score across three dimensions:

**A. Price Performance**
- Calculate price change since the analysis date: `(current_price - prior_price) / prior_price * 100`
- For stocks only (ETFs scored differently — see below)
- Map to a signal:
  - Overweight call + price up >10% = ✅ Correct
  - Overweight call + price flat (-10% to +10%) = 🟡 Mixed
  - Overweight call + price down >10% = ❌ Incorrect
  - At-weight call + price within ±15% = ✅ Correct
  - Underweight call + price down >10% = ✅ Correct
  - Underweight call + price up >15% = ❌ Incorrect
  - (Adjust thresholds if the analysis was made during a high-volatility macro period)

**B. Thesis Validity**
Look at the prior bull/bear cases and assess which materialized:
- Did the bull thesis play out? (revenue growth, catalyst, macro tailwind)
- Did the bear thesis materialize? (risk that was flagged actually hit)
- Were the key risks correct?

Rate: Thesis Held / Partially Held / Thesis Broken

**C. Fundamental Trend**
Compare current key metrics vs prior analysis:
- For stocks: revenue growth direction, margin trend, FCF trend
- For ETFs: AUM change, 3yr return trend, top holdings stability
- Rate: Improving / Stable / Deteriorating

**Overall Retrospective Grade:**
| Price | Thesis | Fundamentals | Grade |
|-------|--------|--------------|-------|
| ✅ | Held | Improving | A |
| ✅ | Held | Stable | B |
| 🟡 | Partially | Any | C |
| ❌ | Broken | Any | D |
| ❌ | Broken | Deteriorating | F |

### Step 4 — Write Retrospective Section to Notion

For each analyzed ticker, append the following section to the existing Notion Analysis Page (prepend before the prior analysis, not after — keep most recent content at top):

```
---
## Retrospective — [current date]
**Analyzed:** [prior analysis date] → Today ([X days elapsed])
**Prior Sizing Signal:** [Overweight / At-weight / Underweight]
**Price at Analysis:** $X.XX | **Current Price:** $X.XX | **Change:** +/-X.X%

**Retrospective Grade:** [A / B / C / D / F]

**Price Score:** [✅ Correct / 🟡 Mixed / ❌ Incorrect]
[1 sentence on what drove the price move or lack thereof]

**Thesis Score:** [Held / Partially Held / Broken]
[2–3 sentences: which elements of the original thesis played out, which didn't]

**What I Got Wrong:** [Be direct — if the thesis was wrong, say what the blind spot was. If the thesis held, write "N/A"]

**Fundamental Trend:** [Improving / Stable / Deteriorating]
[1–2 sentences on key metric changes]

**Updated Sizing Signal:** [Overweight / At-weight / Underweight / Unscored]
[1 sentence rationale — has conviction changed based on what we now know?]
---
```

Also update the Portfolio DB entry for the ticker:
- `date:Last Analyzed:start` = today
- `Sizing Signal` = updated signal from this retrospective

### Step 5 — Portfolio-Level Accuracy Summary

After all individual retrospectives are written, produce a summary:

```
## Retrospective Summary — Q[X] [Year]
**Tickers Reviewed:** X
**Grades:** A: X | B: X | C: X | D: X | F: X

### What Went Right
[Top 2–3 calls that played out — what was the insight that was correct?]

### What Went Wrong
[Top 2–3 misses — what pattern do the errors share? (e.g., "consistently underestimated China risk", "missed rate sensitivity on growth stocks")]

### Systematic Bias Check
[Look across all D/F grades for common factors: sector bias, source bias, macro blind spot]

### Suggested Calibration for Next Cycle
[1–3 specific adjustments to make to future analysis based on the errors found]
```

### Pattern Base Rates

After grading each thesis, resolve it into the calibration database:

```bash
# Tag the note (if not already tagged):
python3 ~/Claude/investing-tool/journal.py tags NOTE_ID rate-sensitive growth-stock

# Log the grade:
python3 ~/Claude/investing-tool/journal.py resolve NOTE_ID --grade B
```

---

## Notes

- Run quarterly — the data quality improves after multiple cycles
- Retrospective honesty is the only way to fix systematic biases — don't soft-pedal wrong calls
- Patterns that repeat (e.g., always too bullish on China) are signals to adjust your process
