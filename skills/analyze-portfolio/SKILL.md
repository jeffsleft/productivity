---
name: analyze-portfolio
description: Fire when running full portfolio analysis or subset analysis, batch analyzing multiple tickers, or running portfolio concentration checks
version: 1.0.0
---

# /analyze-portfolio

## Trigger
- `/analyze-portfolio` — runs analysis on the full active portfolio (all Status=Active tickers in the Portfolio DB)
- `/analyze-portfolio TICKER1 TICKER2 ...` — runs analysis on a specified subset only

## What This Skill Does
Batch runner for `/analyze-stock`. Iterates through the portfolio (or a subset), runs the full analysis on each ticker, writes all results to Notion, then runs a portfolio-level concentration check. Updates all Portfolio DB entries with fresh Sizing Signal and Last Analyzed values.

This skill is intentionally designed to be run when you have time — a full portfolio run across 78 tickers is a long operation. For regular use, prefer `/analyze-stock TICKER` for individual deep dives.

---

## Step-by-Step Execution

### Step 1 — Determine Scope

**Full portfolio:**
- Query the Portfolio DB (data_source_id: `61b7f3e7-0300-498d-ab47-cd36a413ed88`) for all entries where Status = "Active"
- Load `~/Claude/investing-tool/notion_page_ids.json` for page ID lookups

**Subset (tickers specified):**
- Use only the tickers provided in the invocation
- Confirm each is in the Portfolio DB; warn if any are not found

Before starting, tell the user:
- How many tickers will be analyzed
- Estimated time (roughly 1–2 min per ticker for web searches + Notion writes)
- Offer to run a smaller test subset first if the full count is >20

### Step 2 — Batch Analysis Loop

For each ticker in scope, in sequence (not parallel — Alpha Vantage has a 25 req/day limit):

1. Determine asset type (Stock / ETF / ETF-Leveraged / Mutual Fund / Money Market) from the Portfolio DB entry
2. Run `python3 ~/Claude/investing-tool/sa_fetcher.py TICKER [--etf TICKER]`
3. Load SA ratings from `~/Claude/investing-tool/sa_ratings.json`
4. Run two parallel WebSearches: recent news + (CEO for stocks / ETF holdings for ETFs)
5. Query Notion Source Library for relevant entries (batch: use sector + ticker as query)
6. Synthesize analysis using the same template as `/analyze-stock`
7. Write to Notion Analysis Pages (create or prepend dated section)
8. Update Portfolio DB entry: Last Analyzed = today, Sizing Signal = signal from analysis

After each ticker, print a one-line progress status:
`✓ [TICKER] — [Sizing Signal] — Notion page [created/updated]`

**Alpha Vantage limit**: Only 25 cross-checks per day. Skip AV cross-check after the 25th stock (note in that ticker's output). ETFs never use AV — they don't count against the limit.

**Error handling**: If a ticker fetch fails (network error, yfinance gap), log the error, skip that ticker, continue to the next. Report all skipped tickers in the final summary.

### Step 3 — Run Macro Snapshot (once, at the start)

Run `python3 ~/Claude/investing-tool/sa_fetcher.py --macro` once before the batch loop begins. Use the same macro data for all tickers in this run (don't re-fetch per ticker — FRED data doesn't change intraday).

### Step 4 — Concentration Check

After all individual analyses are complete, run the portfolio concentration check:

**Sector concentration:**
- Tally all active holdings by Sector (from Portfolio DB)
- Flag any sector where holdings exceed 30% of the total active ticker count
- Note: this is count-based, not dollar-weighted (we don't have position sizes)

**ETF overlap check:**
- For any ticker that appears in the top 10 holdings of multiple ETFs in the portfolio, flag it
- Pull top holdings from the `last_fetch.json` outputs for all ETFs analyzed in this run
- Cross-reference: if a stock (e.g., TSLA) appears in BATT, LIT, and is also held directly — flag the concentration
- Threshold: flag any individual company that appears in 3+ ETFs or holds a direct position + 2+ ETFs

**Leveraged ETF flag:**
- List all ETF-Leveraged holdings (FAS, TECL, TNA, CURE, UBOT) separately
- Note their combined exposure as a risk flag: leveraged ETFs are not suitable as long-term holds and decay over time in sideways markets

**Output format:**
```
## Portfolio Concentration Report — [date]

### Sector Concentration
| Sector | Count | % of Portfolio | Flag |
|--------|-------|----------------|------|
| Technology | 12 | 31% | ⚠️ Over 30% |
| ...

### Top ETF Overlap — Holdings Appearing in 3+ Vehicles
| Company | Appears In |
|---------|-----------|
| Tesla | BATT, LIT, VXUS, direct holding |

### Leveraged ETF Risk Flag
Active leveraged ETFs: FAS (3x Financials), TECL (3x Tech), TNA (3x Small Cap), CURE (3x Healthcare), UBOT (3x Robotics)
These are decay-risk positions in low-volatility or sideways markets. Review sizing.
```

### Step 5 — Final Summary

After the full run, produce:
- Count: X tickers analyzed, Y skipped (list reasons)
- Breakdown by Sizing Signal: X Overweight, Y At-weight, Z Underweight, W Unscored
- Top 3 highest-conviction tickers from this run (highest quant rating + Overweight signal)
- Top 3 lowest-conviction / flagged tickers (Underweight signal or key risks flagged)
- Concentration report (from Step 4)
- Reminder: "Run `/add-source` with any new macro reports or transcripts to improve the next analysis cycle"

---

## Important Constraints

- **Run time**: A full 78-ticker run will take considerable time. Always give the user an upfront estimate and offer a subset option.
- **No parallel fetching**: Run tickers sequentially to respect Alpha Vantage's 25 req/day limit.
- **Sizing Signal is relative**: The signal for each ticker should be calibrated relative to the rest of the portfolio — not absolute. After all analyses are done, do a quick pass to ensure signals are consistent (e.g., not everything is Overweight).
- **Money Market tickers** (VMFXX, VUSXX): Skip analysis entirely for these — note them as "Cash/Money Market — no analysis needed."
- **ETF-Leveraged tickers**: Analyze using ETF template but always add a standard leveraged decay risk note to the Bear Case.
- **Alpha Vantage**: Track usage count. After 25 stock cross-checks, skip AV and note it.
