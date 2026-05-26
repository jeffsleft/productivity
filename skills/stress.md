# /stress

## Trigger
`/stress` — run on demand or at each quarterly retrospective.

Optionally: `/stress 2022` to run a single regime only.

## What This Skill Does
Stress-tests the current portfolio against 4 historical crash regimes (2000, 2008, 2020, 2022). Pulls historical prices via yfinance, computes weighted drawdown per regime, caches results in SQLite, and outputs a summary table comparing this portfolio to S&P 500 actual drawdowns.

The goal: know in advance how bad it could get. Not prediction — calibration.

---

## Step-by-Step Execution

### Step 1 — Fetch Portfolio Weights from Notion

Query the Portfolio DB (data_source_id: `61b7f3e7-0300-498d-ab47-cd36a413ed88`) to get all current holdings with their market values.

For each holding, extract:
- Ticker symbol
- Current market value (or shares × current price if value isn't stored directly)

Compute each position's weight: `weight_pct = position_value / total_portfolio_value * 100`

Skip: VMFXX, VUSXX, and any money market / cash tickers.

If Notion MCP is unavailable, fall back to equal-weight mode (Step 2b).

### Step 2a — Write Weights File

Write the computed weights to a temp file:

```bash
cat > /tmp/portfolio_weights.json << 'EOF'
{
  "AAPL": 5.2,
  "MSFT": 7.1,
  "GTLB": 12.3,
  ...
}
EOF
```

### Step 2b — Equal-Weight Fallback

If portfolio values are unavailable, use equal-weight mode. Note this assumption prominently in the output.

```bash
cd ~/Claude/investing-tool && python3 stress.py run --equal-weight
```

### Step 3 — Run Stress Test

```bash
cd ~/Claude/investing-tool && python3 stress.py run --weights /tmp/portfolio_weights.json
```

For a single regime:
```bash
cd ~/Claude/investing-tool && python3 stress.py run --weights /tmp/portfolio_weights.json --regime 2022
```

This fetches prices for each ticker at the peak and trough of each regime, computes drawdown and weighted drawdown, and writes results to `investing.db` (`stress_results` table).

**Runtime note:** Fetching prices for 4 regimes × N tickers takes 1–3 minutes due to yfinance rate limiting. Normal.

### Step 4 — Interpret and Display Results

The script prints the full report. Synthesize it for Jeff with these additions:

**Key context to add:**
- Which positions would have been the worst hit in each regime? Why (growth vs. value, leverage, sector)?
- Where does this portfolio compare to S&P 500 in each scenario — more or less exposed?
- Any surprise positions (e.g., a "defensive" name that actually dropped hard in 2020)?

**Flags to call out:**
- Any position with drawdown worse than -60% in any regime → flag as concentration risk
- Portfolio total worse than S&P by >10pts in any regime → flag as above-market risk
- 2022 regime specifically: rate-sensitive growth names and leveraged ETFs will take the largest hits — call this out if present

### Step 5 — Cache Status

After the run, confirm:
```bash
cd ~/Claude/investing-tool && python3 stress.py view
```
Results are cached — the quarterly retrospective and Spouse Digest can reference the most recent run without re-fetching.

---

## Output Summary Format

When reporting to Jeff, lead with this table (pulled from the script output):

```
## Portfolio Stress Test — [run date]

| Regime | This Portfolio | S&P 500 | vs S&P |
|--------|---------------|---------|--------|
| 2000: Dot-Com Crash | -X.X% | -49.1% | +/-X.Xpts |
| 2008: Global Financial Crisis | -X.X% | -56.8% | +/-X.Xpts |
| 2020: COVID Crash | -X.X% | -33.9% | +/-X.Xpts |
| 2022: Rate Hike Bear Market | -X.X% | -25.4% | +/-X.Xpts |

**Worst single position across all regimes:** [TICKER] (-X.X% in [regime])
**Most resilient position:** [TICKER] ([drawdown] in worst regime)
**Key takeaway:** [1–2 sentences — is this portfolio more or less volatile than the market? Any structural concentration risks revealed?]
```

---

## Important Constraints

- **No prediction:** Stress tests are calibration tools, not forecasts. The 2000 scenario won't repeat exactly — but the magnitude of what a growth-heavy portfolio can lose in a multi-year bear market is the useful signal.
- **Tickers that post-date a regime:** If a company IPO'd after 2008, there's no 2008 price data. The script notes these as "no data." The portfolio drawdown is computed on covered weight only — the coverage % is shown.
- **Leveraged ETFs in 2000/2008:** These didn't exist then. They'll show "no data." Manual proxies (2× the underlying's drawdown as a rough estimate) can be noted in the synthesis.
- **ETFs with short history:** Some sector ETFs may not have 2000 data. Use the S&P or sector benchmark as context.
- **Weights matter:** Equal-weight mode significantly understates risk if GTLB (or another large position) dominates the actual portfolio. Always prefer actual weights.
- **GTLB note:** Given GTLB's current outsized weight, its performance in the 2022 rate-hike regime is particularly relevant — high-multiple SaaS was crushed. Call this out explicitly.

---

## Recommended Cadence

- Run once per quarter (auto-invoked at the end of `/retrospective`)
- Run ad hoc after any major macro event (Fed rate decision, geopolitical shock, etc.)
- Always re-run after a significant portfolio rebalance

View cached results at any time:
```bash
cd ~/Claude/investing-tool && python3 stress.py view
python3 stress.py view 2022   # single regime
python3 stress.py regimes     # see regime definitions
```
