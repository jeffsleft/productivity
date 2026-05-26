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
[Look across all D/F grades for common factors:
- Sector bias? (e.g., always too bullish on tech)
- Source bias? (e.g., SA Quant alone was a misleading signal in X cases)
- Macro blind spot? (e.g., dollar strength systematically hurt international ETF calls)
]

### Suggested Calibration for Next Cycle
[1–3 specific adjustments to make to future analysis based on the errors found]

### Pattern Base Rates (V2-L)
After grading each thesis, resolve it into the calibration database:

```bash
# Tag the note (if not already tagged):
python3 ~/Claude/investing-tool/journal.py tags NOTE_ID rate-sensitive growth-stock

# Log the grade:
python3 ~/Claude/investing-tool/journal.py resolve NOTE_ID --grade B
```

Then run the full patterns report:
```bash
python3 ~/Claude/investing-tool/journal.py patterns
```

Include the output's "Overall" and "Pattern Base Rates" sections in the retrospective summary.
If fewer than 10 resolved predictions exist, note "Calibration data thin — [N] predictions resolved to date."

### Stress Test Snapshot (V2-K)
Pull the most recent stress test results from SQLite. If the cache is from a prior quarter, re-run first.

```bash
cd ~/Claude/investing-tool && python3 stress.py view
```

If no cached results or stale:
```bash
# First write portfolio_weights.json from Notion Portfolio DB values
python3 stress.py run --weights /tmp/portfolio_weights.json
```

Include this table in the summary output:
```
| Regime | This Portfolio | S&P 500 | vs S&P |
|--------|---------------|---------|--------|
| 2000: Dot-Com Crash           | -X.X% | -49.1% | ±X.Xpts |
| 2008: Global Financial Crisis | -X.X% | -56.8% | ±X.Xpts |
| 2020: COVID Crash             | -X.X% | -33.9% | ±X.Xpts |
| 2022: Rate Hike Bear Market   | -X.X% | -25.4% | ±X.Xpts |
```

[1 sentence: is this portfolio more or less exposed than the market? Call out any regime where it's notably worse.]
```

---

## Important Constraints

- **60-day minimum**: Don't retrospect on analyses less than 60 days old — the signal is too noisy.
- **No prior price in Notion**: The prior price at the time of analysis may not be stored explicitly. Check the Quantitative Snapshot section of the prior analysis page for the price row. If not found, use the SA ratings file price field as a proxy (it reflects the price at Excel export time).
- **ETF price scoring**: For ETFs, a ±15% band is the neutral zone (higher volatility expected). Adjust thresholds accordingly.
- **Leveraged ETF note**: Decay is expected in leveraged ETFs over time. Factor this into the grade — an TECL or FAS Underweight that fell due to decay is not a failed call, it's expected behavior.
- **Money Market / Cash tickers**: Skip VMFXX and VUSXX entirely.
- **Grade honestly**: The retrospective is useless if it's generous. A broken thesis should get a D or F even if the position happened to go up due to unrelated factors.
- **SA ratings freshness**: Update `sa_ratings.json` by running a new Excel export from SeekingAlpha before each quarterly retrospective. The ratings may have changed significantly.

---

---

## Step 6 — Road Not Taken (not_bought / not_sold)

Run this after Step 5. It scores decisions where you considered acting but didn't.

### Step 6a — Find Eligible Entries

Query SQLite for entries that are ≥60 days old (or past their prediction horizon) with no outcome recorded:

```bash
python3 ~/Claude/investing-tool/journal.py list --type not_bought --days 3650
python3 ~/Claude/investing-tool/journal.py list --type not_sold --days 3650
```

Or query directly:
```sql
SELECT id, ticker, date, decision_type, price, thesis, prediction_horizon, outcome_note
FROM investment_notes
WHERE decision_type IN ('not_bought', 'not_sold')
  AND outcome_note IS NULL
  AND (
    date <= date('now', '-60 days')
    OR (
      prediction_horizon IS NOT NULL
      AND (
        (prediction_horizon = '1mo'  AND date <= date('now', '-30 days'))
        OR (prediction_horizon = '3mo'  AND date <= date('now', '-91 days'))
        OR (prediction_horizon = '6mo'  AND date <= date('now', '-182 days'))
        OR (prediction_horizon = '1yr'  AND date <= date('now', '-365 days'))
        OR (prediction_horizon = '2yr'  AND date <= date('now', '-730 days'))
        OR (prediction_horizon = '3yr'  AND date <= date('now', '-1095 days'))
        OR (prediction_horizon = '5yr'  AND date <= date('now', '-1825 days'))
      )
    )
  )
ORDER BY date ASC;
```

Skip any entry where price at decision was NULL — no baseline to score against.

### Step 6b — Fetch Current Prices

For each eligible ticker, run `sa_fetcher.py` (or use the price already fetched in Step 2 if the ticker overlaps with an active portfolio position).

### Step 6c — Score Each Decision

**not_bought scoring** (you passed on buying — was that right?):
- Price up >15% since decision → ❌ Costly miss (opportunity cost)
- Price ±15% → 🟡 Neutral (pass was defensible)
- Price down >15% since decision → ✅ Correct pass (avoided a loser)

**not_sold scoring** (you considered selling but held — was that right?):
- Price up >10% since decision → ✅ Correct hold
- Price ±10% → 🟡 Neutral
- Price down >10% since decision → ❌ Should have sold

Use the entry's `price` field as the baseline. If that field is NULL, skip scoring and note it as "No baseline price."

**Adjust thresholds for macro context:** If the entry date falls inside a high-volatility period (e.g., a ≥10% market drawdown in that quarter), widen bands by 5 percentage points before grading.

### Step 6d — Produce Road Not Taken Output

For each entry, output this block:

```
---
## Road Not Taken — [TICKER] ([not_bought / not_sold]) — [current date]
**Decision Date:** [date] | **Decision Price:** $X.XX | **Current Price:** $X.XX
**Change Since Decision:** +/-X.X% ([X days elapsed])

**Call Grade:** [✅ Right / 🟡 Neutral / ❌ Wrong]

**What Happened:**
[1–2 sentences: did the stock do what you feared / hoped, or the opposite?
Reference any known catalysts — earnings, macro, sector move.]

**Original Reasoning:**
[Pull the thesis field from the journal entry verbatim — do not paraphrase.]

**Lessons Learned:**
[Was the reasoning sound even if the outcome went the other way? What would you
do differently? If the call was right, what made the analysis correct?]

**Record outcome:** python3 ~/Claude/investing-tool/journal.py outcome [id]
---
```

After output, prompt Jeff to run `journal.py outcome <id>` for each entry so the outcome is logged and that entry won't surface again next quarter.

### Step 6e — Notion Sync (optional)

If Jeff wants to file the Road Not Taken retrospective in Notion, create a new page in the Trade & Belief Journal DB (`c40af2a18a5a42c9bcacb23c3c94f9f3`) titled:

`Road Not Taken Retro — [TICKER] — [Quarter] [Year]`

Fields:
- Decision Type: `not_bought` or `not_sold`
- Date: original decision date
- Thesis: original thesis
- Retro Grade: Right / Neutral / Wrong
- Retro Notes: the "What Happened" and "Lessons Learned" text

This is optional — do it only if Jeff asks or if the grade is ❌ (wrong calls are worth filing for pattern-tracking).

### Step 6f — Road Not Taken Summary

Append this block to the Step 5 Portfolio-Level Accuracy Summary:

```
### Road Not Taken — Q[X] [Year]
**Entries Reviewed:** X  (not_bought: X | not_sold: X)
**Grades:** ✅ Right: X | 🟡 Neutral: X | ❌ Wrong: X

**Biggest Misses (❌):**
[List each wrong call: TICKER, decision type, % move against you, 1-line lesson]

**Best Passes / Holds (✅):**
[List each right call: TICKER, decision type, % move in your favor, 1-line note]

**Pattern Check:**
[Do the wrong calls share a theme? E.g., "Consistently passed on momentum names
that ran" or "Held positions too long after thesis inflection signals appeared"?
If fewer than 3 wrong calls, write "Insufficient data for pattern analysis."]
```

---

## Step 7 — Conviction Drift Alert (V2-F)

Run this at the end of every retrospective, after Steps 5 and 6. Takes ~2 minutes. Flags any position where the Sizing Signal has gone stale relative to current data.

### Step 7a — Load Portfolio Signals

Fetch all portfolio entries from the Portfolio DB (data_source_id: `61b7f3e7-0300-498d-ab47-cd36a413ed88`). For each ticker, extract:
- `Sizing Signal` (Overweight / At-weight / Underweight / Unscored)
- `Last Analyzed` date
- `Last Analyzed` price (from the Quantitative Snapshot in the Analysis Page, if stored — use the SA ratings file price as a fallback)

Skip VMFXX and VUSXX.

### Step 7b — Load Current SA Ratings

Read `~/Claude/investing-tool/sa_ratings.json`. For each ticker, extract the current `quant_rating`. If a ticker is not in the file, note "SA rating unavailable" and skip rating-based checks for that ticker.

The prior quant rating (at time of last analysis) is not stored explicitly. Use the value in the most recent Retrospective section of the Analysis Page if it exists, otherwise treat the prior rating as unknown and skip rating-based checks.

### Step 7c — Check Alert Conditions

For each portfolio ticker, check these three conditions. Any single condition triggers an alert:

1. **Rating collapse** — SA Quant dropped >1.0 since last analysis AND Sizing Signal = Overweight
2. **Stale + moved** — Last Analyzed date is >120 days ago AND current price has moved >20% from the price at last analysis (either direction)
3. **Below floor** — SA Quant Rating is now <2.0 AND Sizing Signal ≠ Underweight

For condition 2, compute `price_delta = (current_price - prior_price) / prior_price * 100`. If prior price is unavailable, check only the >120-day staleness and flag with "price unknown."

### Step 7d — Output

**If alerts exist**, output this table in the retrospective summary:

```
## Stale Conviction Alerts — [Quarter] [Year]

| Ticker | Signal | Last Analyzed | Quant Then → Now | Price Δ | Condition | Action |
|--------|--------|---------------|------------------|---------|-----------|--------|
| GTLB   | Overweight | 2026-01-10 | 3.2 → 1.16 | -31% | Rating collapse | Re-analyze urgently |
| AMZN   | At-weight  | 2025-12-01 | 3.8 → 3.6  | +28% | Stale + moved   | Re-analyze         |
| XYZ    | Overweight | 2026-02-14 | N/A → 1.8  | -5%  | Below floor     | Review signal      |
```

Action labels:
- Rating collapse → "Re-analyze urgently"
- Stale + moved → "Re-analyze"
- Below floor → "Review signal"
- Multiple conditions on same ticker → use highest-priority action ("Re-analyze urgently" > "Re-analyze" > "Review signal")

**If no alerts:**

```
## Stale Conviction Alerts — [Quarter] [Year]
✓ No stale conviction signals. All Sizing Signals are current and consistent with SA ratings.
```

---

## Recommended Cadence

Run at the start of each quarter:
- Q1: First week of April
- Q2: First week of July
- Q3: First week of October
- Q4: First week of January

Before each run: export a fresh SA portfolio Excel and re-parse it to update `sa_ratings.json`.
