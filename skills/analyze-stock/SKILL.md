---
name: analyze-stock
description: Fire when analyzing a single stock ticker, ETF, or fund, including Contrarian Source Gate checks and Risk Cap Gate evaluation
version: 1.0.0
---

# /analyze-stock

## Trigger
User types `/analyze-stock TICKER` or `/analyze-stock TICKER [optional notes or pasted SA article text]`

## What This Skill Does
Deep-dive analysis of a single stock, ETF, or fund. Pulls live fundamentals + macro data, loads SA ratings, searches for news and leadership signals, synthesizes everything into a structured investment thesis (with a hard Contrarian Source Gate enforcing a non-SA bear thesis), and writes the output to Notion Analysis Pages.

---

## Step-by-Step Execution

### Step 1 — Parse Input
- Extract `TICKER` from the invocation (uppercase it)
- Check if the user pasted any SeekingAlpha article text after the ticker — if so, save it as `SA_ARTICLE_TEXT` for use in synthesis
- If no SA article text is present, note that SA article analysis will be skipped (ratings only)

### Step 2 — Determine Asset Type
Check `~/Claude/investing-tool/notion_page_ids.json` to confirm the ticker exists in the portfolio.
Then determine type from the Notion Portfolio DB (Stock / ETF / ETF-Leveraged / Mutual Fund / Money Market).
If the ticker is not in the portfolio, treat it as an ad-hoc analysis and infer type from yfinance output.

### Step 3 — Fetch Live Data
Run the fetcher script:
```
python3 ~/Claude/investing-tool/sa_fetcher.py TICKER
```
For ETFs add the `--etf` flag:
```
python3 ~/Claude/investing-tool/sa_fetcher.py TICKER --etf TICKER
```
Also always fetch current macro snapshot:
```
python3 ~/Claude/investing-tool/sa_fetcher.py --macro
```
Read the output from `~/Claude/investing-tool/last_fetch.json`.

**Cross-check flag**: If yfinance and Alpha Vantage differ by >15% on P/E or net margin, flag it explicitly in the Quantitative Snapshot.

### Step 4 — Load SA Ratings
Read `~/Claude/investing-tool/sa_ratings.json` and extract the entry for TICKER.
Fields: `quant_rating`, `sa_analyst_rating`, `wall_street_rating`
If the ticker is not in the file, note "SA ratings not available — add via /add-source or next Excel export."

### Step 5 — Search News & Leadership
Run two WebSearches in parallel:
1. `"TICKER" stock news 2026` — recent headlines, earnings, guidance, analyst actions
2. `"TICKER" CEO leadership management 2026` — for stocks only (skip for ETFs/funds)

For ETFs/leveraged ETFs, replace the CEO search with:
- `"TICKER" ETF holdings rebalance expense ratio 2026`

### Step 5b — Load CEO Scorecard (stocks only)
Query the SQLite DB for the most recent CEO scorecard for this ticker:
```python
import sqlite3, json
from pathlib import Path
DB_PATH = Path.home() / "Claude/investing-tool/investing.db"
conn = sqlite3.connect(DB_PATH)
conn.row_factory = sqlite3.Row
row = conn.execute(
    "SELECT * FROM ceo_scorecards WHERE ticker = ? ORDER BY scored_at DESC LIMIT 1",
    (TICKER,)
).fetchone()
conn.close()
```
**If a scorecard exists:** extract composite, tenure_tag, industry_trajectory, hard_flags, soft_flags, notes, scored_at. Use this in the CEO section of the output template (Step 8). Note the score age — if >180 days old, flag it as stale.

**If no scorecard exists:** note "No CEO scorecard on file — run `/ceo-score [TICKER]` to generate one." Include the web search findings from Step 5 in the CEO section as a manual narrative instead.

Skip for ETFs/funds.

### Step 6 — Load Risk Rules + Strategy Framework

Load both files:

**`~/Claude/investing-tool/risk_rules.json`** — machine-readable caps (authoritative for numbers):
- `single_stock.entry_cap_pct` (8%) and `single_stock.review_trigger_pct` (15%)
- `sector.alert_pct` (25%) and `sector.hard_limit_pct` (30%)
- `leveraged_etf.combined_cap_pct` (10%) and `leveraged_etf.review_trigger_days` (180)
- `checklist.overweight_pass_threshold` (3 of 5)
- `no_fly_zones` list
- `blocked_signal_conditions` and `warning_signal_conditions` lists

**`~/Claude/investing-tool/strategy_framework.md`** — narrative context, sector stances, account strategy, and the 5-item checklist rules. Use this for qualitative framing in the Strategy Alignment Check section.

You will use both in Step 8 to run the Risk Cap Gate and generate the Strategy Alignment Check section.

### Step 7 — ~~Query Notion Source Library~~ (deprecated 2026-05-14)

The Source Library DB was a V1 stub that was never populated. As of 2026-05-14, it is no longer queried. The DB itself remains in Notion (empty) for possible future revival; the `add-source` skill is similarly preserved but inactive.

**Replacement:** Non-SA contrarian material now flows through Step 7.5 (Contrarian Source Gate) — bear-thesis sourcing comes from `[News]` web search, `[CEO Flags]`, `[Macro Data]`, or `[User-Provided]` rather than a curated DB.

If/when you decide to revive the Source Library (build it for real), restore this step to its previous form (query `dd31ba38-af4a-4c23-af79-a3bf4131c0b2` for relevant entries within the last 6 months) and re-introduce the `[Source Library]` source class to the gate inventory.

### Step 7.5 — ⛔ Contrarian Source Gate (HARD GATE — do not skip)

**Purpose:** Prevent SA-monoculture in the Bear Case. SeekingAlpha is the primary signal but should never be the only voice — its bear theses are well-known and already priced in.

**Before proceeding to Step 8, inventory your bear-thesis sources from Steps 1, 5, 5b, 6, and 7.** Label each:

| Source class | Examples | Counts as non-SA? |
|---|---|---|
| `[SA]` SeekingAlpha-derived | `SA_ARTICLE_TEXT` user pasted, sa_ratings.json scores, anything quoted from SA authors | ❌ No |
| `[News]` Web search results | Bloomberg, WSJ, Reuters, FT, sector trade press, blogs (non-SA) | ✅ Yes |
| `[CEO Flags]` | hard_flags / soft_flags from the CEO scorecard | ✅ Yes |
| ~~`[Source Library]`~~ | *(deprecated — Source Library DB no longer queried as of 2026-05-14)* | n/a |
| `[Macro Data]` | FRED indicators showing headwinds for this ticker | ✅ Yes |
| `[User-Provided]` | A bear quote/URL/summary the user pasted *that is not SA* | ✅ Yes |

**Gate condition:**
- At least **one** non-SA source must supply bear-relevant material. (Doesn't need to be a full thesis — a single specific headwind cited from a non-SA source counts.)

**If the gate fails (no non-SA bear material available):**

**STOP.** Do NOT proceed to Step 8. Output this message to the user verbatim:

```
⛔ Contrarian Source Gate — cannot complete analysis

I have bear-case material from SeekingAlpha but no non-SA sources contradicting the bull thesis.
SA's bear case is already priced in. Per Chunk C.1, I need at least one non-SA contrarian source
before finalizing the analysis.

Options to unblock:
1. Paste a non-SA URL or quote with bear-case material (Bloomberg article, short-seller note,
   sector trade press, alternative analyst report, your own bear thesis, etc.)
2. Tell me which non-SA news/CEO-flag/macro item from the data I already have qualifies as
   contrarian, and I'll proceed with that
3. Type "override" if you have reason to skip this gate (I'll log it as a Pre-Mortem note)
```

Wait for the user's response, then loop back through Step 7.5 with the new material before continuing.

**If the user types "override":** Proceed to Step 8 BUT add a `> ⚠️ Contrarian Source Gate overridden by user — bear case is SA-derived only` callout at the top of the Bear Case section in the output, and pre-fill a Pre-Mortem entry in the journal noting this.

### Step 8 — Synthesize Analysis

Produce the full structured analysis. Use the template below. Be direct and opinionated — avoid hedged non-conclusions.

---

## Output Template

```
# [TICKER] — [Company/Fund Name]
**Analysis Date:** [today's date]
**Type:** [Stock / ETF / ETF-Leveraged / Mutual Fund]
**Sector:** [sector]
**SA Quant Rating:** [X.XX] | **SA Analyst:** [X.XX] | **Wall Street:** [X.XX]

---

## Quantitative Snapshot

| Metric | Value | Source |
|--------|-------|--------|
| Price | $X.XX | yfinance |
| 52W Range | $X.XX – $X.XX | yfinance |
| Market Cap | $XB | yfinance |
| P/E (TTM) | X.X | yfinance |
| Forward P/E | X.X | yfinance |
| Revenue Growth (YoY) | X.X% | yfinance |
| Gross Margin | X.X% | yfinance |
| Net Margin | X.X% | yfinance |
| Free Cash Flow | $XM | yfinance |
| Debt/Equity | X.XX | yfinance |
| Beta | X.XX | yfinance |
| Dividend Yield | X.X% | yfinance |

[If AV cross-check flags discrepancy: ⚠️ AV vs yfinance discrepancy on [metric]: [yf value] vs [av value] — verify before sizing]

**For ETFs only — replace fundamentals table with:**
| Metric | Value |
|--------|-------|
| AUM | $XB |
| Expense Ratio | X.XX% |
| Category | [category] |
| YTD Return | X.X% |
| 3-Year Return | X.X% |
| 5-Year Return | X.X% |
| Top 3 Holdings | [name (X%), name (X%), name (X%)] |

---

## Macro Context

[2–3 sentences that directly connect current macro data to THIS company's revenue, margins, or valuation multiple. Use FRED data from Step 3's `--macro` snapshot.

**Validation rules (the Step 8a Macro Specificity Validator will check):**
- Must mention TICKER explicitly (or "this company" / company name) — not generic "growth stocks" / "the market"
- Must cite at least one specific FRED indicator with current value (e.g., "FEDFUNDS at 5.25%", "CPI YoY at 3.2%", "10Y at 4.5%", "UNRATE at 4.0%", "Dollar Index at 105")
- Must connect that indicator to a specific business effect: revenue line, gross margin, net margin, valuation multiple, FCF yield, or input costs — name the mechanism, not just "headwind / tailwind"

**Examples of acceptable phrasing:**
- "FEDFUNDS at 5.25% compresses TICKER's terminal multiple because it trades at 28× fwd P/E with 2.5% FCF yield — a 1 pt rate move historically moves the multiple 2-3 turns."
- "CPI YoY at 3.2% supports TICKER's pricing power given the 380 bps gross margin expansion over the past 4 quarters, but a sub-2% print would reverse that tailwind."

**Examples that will fail validation (regenerate if seen):**
- "Rising rates are bad for growth stocks." (generic, no ticker, no value)
- "AI demand is strong, which helps." (no FRED indicator, no mechanism)
- "Macro headwinds may impact performance." (handwave)
- "The Fed could cut rates, which would be positive." (no value, no mechanism)]

---

## Bull Case

[3–5 specific arguments. Lead with the strongest. Must be grounded in data from the fetch or news. If SA article was provided, incorporate the bull thesis from it.]

1. **[Argument title]** — [specific supporting data]
2. **[Argument title]** — [specific supporting data]
3. **[Argument title]** — [specific supporting data]

---

## Bear Case

[3–5 specific counter-arguments. Mandatory — not optional. This section must exist even for high-conviction holdings. Helps identify what would invalidate the thesis.

**Source labeling required.** Tag each argument with its source class from the Contrarian Source Gate inventory:
`[SA]`, `[News]`, `[CEO Flags]`, `[Macro Data]`, `[User-Provided]`.
**At least one argument MUST be tagged with something other than `[SA]`** (this was already verified by the Step 7.5 gate — restate the source labels here so the reader can audit).]

1. **[Risk title]** `[Source label]` — [specific concern and what to watch]
2. **[Risk title]** `[Source label]` — [specific concern and what to watch]
3. **[Risk title]** `[Source label]` — [specific concern and what to watch]

---

## CEO / Leadership [STOCKS ONLY — omit for ETFs/funds]

**CEO:** [name] | Tenure: [X yrs] — [Tenure Tag] | **Scorecard: [composite]/100** (scored [date])

| Dimension | Score | Weight |
|---|---|---|
| Capital Allocation | XX/100 | 40% |
| Execution & Reliability | XX/100 | 25% |
| Talent & Culture | XX/100 | 20% |
| Communication & Integrity | XX/100 | 10% |
| Board Quality | XX/100 | 5% |

**Industry Trajectory:** [Rising/Stable/Declining/Mixed] — [evidence]
**Flags:** [hard flags] / [soft flags] or "none"

[2–3 sentences synthesizing scorecard + current news. Be direct — is this a high-quality operator or a concern? If no scorecard exists, lead with that and provide a qualitative assessment from Step 5 web research. If scorecard is >180 days old, flag it as stale.]

*No scorecard? Run `/ceo-score [TICKER]` to generate one.*

---

## ETF / Fund Profile [ETFs/FUNDS ONLY — replace CEO section]

### Strategy & Mandate
**Index tracked / Active approach:** [e.g., "Tracks NASDAQ-100", or "Actively managed, mid-cap growth", or "Smart beta — momentum tilt"]
**Inception:** [date] | **AUM:** $XB | **Avg daily volume:** $XM | **Bid-ask spread (typical):** X bps

### Top 10 Holdings
| # | Holding | % of Fund | Sector |
|---|---|---|---|
| 1 | [name] | X.X% | [sector] |
| 2 | [name] | X.X% | [sector] |
| ... | ... | ... | ... |
| 10 | [name] | X.X% | [sector] |

**Top 10 concentration:** X% of fund | **Top 3 concentration:** X% | **Effective holdings (1/HHI):** [N]
*(If top 10 > 50%, flag as concentrated. If effective holdings < 30, flag as low diversification regardless of nominal count.)*

### Factor Tilt
Classify against the 6 standard factors. State each as **Strong / Moderate / Neutral / Negative** with one-line evidence:

| Factor | Tilt | Evidence |
|---|---|---|
| **Value** (low P/E, low P/B) | [tilt] | [weighted avg P/E vs S&P 500] |
| **Growth** (high EPS / revenue growth) | [tilt] | [weighted EPS growth vs broad market] |
| **Momentum** (12-1 month price return) | [tilt] | [trailing return rank] |
| **Quality** (high ROE, low debt, stable earnings) | [tilt] | [composite quality score] |
| **Size** (small vs large cap) | [tilt] | [weighted avg market cap] |
| **Low Volatility** (low beta, low realized vol) | [tilt] | [weighted beta, 1Y std dev] |

**Dominant tilt:** [name the single most defining factor — that's the bet you're making by owning this fund.]

### Costs & Operational
**Expense Ratio:** X.XX% — [cheap / fair / expensive vs category avg X.XX%]
**Tracking error vs benchmark:** X bps (if passive)
**Tax efficiency:** [high — uses in-kind redemptions / low — high turnover causes distributions]
**Securities lending revenue:** [offsets fees if substantial]

### Fund Flows (last 4 quarters)
| Quarter | Net Flow | AUM Change | Signal |
|---|---|---|---|
| Most recent | [+/-$XM] | [+/-X%] | [accumulation / distribution / neutral] |
| Q-1 | ... | ... | ... |
| Q-2 | ... | ... | ... |
| Q-3 | ... | ... | ... |

*Persistent outflows + price weakness = capitulation signal (contrarian buy) or thesis-breakage (avoid). Persistent inflows + price strength = momentum / fashion. Note which pattern applies.*

### Portfolio Overlap Check
Query the Notion Portfolio DB for other ETFs/funds you hold, then quantify holdings overlap:

| Other holding | Overlap % | Combined exposure if both held at X%, Y% |
|---|---|---|
| [TICKER1] | X% | Top 5 shared names with combined weight |
| [TICKER2] | X% | ... |

**Flag if:** any pairwise overlap >50% (you effectively own one fund twice) OR combined top-5 names exceed `risk_rules.json` single-name 8% entry cap when both ETFs are held at proposed sizes.

**Mutual fund variant:** If actively managed, also note manager tenure, manager-name change in last 3 years (red flag), and any style drift (e.g., "value fund holding NVDA").

---

## Key Risks

1. [Specific, non-generic risk]
2. [Specific, non-generic risk]
3. [Specific, non-generic risk]

---

## Conviction Rank & Sizing Signal

**Sizing Signal:** [Overweight / At-weight / Underweight / Unscored]
**Rationale:** [1–2 sentences explaining the sizing signal relative to portfolio. This is a relative ranking, not an absolute 0–10 score. Compare to similar positions.]

---

## ⛔ Risk Cap Gate

*Check `risk_rules.json` caps before finalizing the Sizing Signal. BLOCKED conditions must be resolved — don't bury them in the Strategy Alignment table.*

**Evaluate each blocked condition:**

1. Single-stock position would exceed **15%** at proposed size → ⛔ BLOCKED
2. Sector concentration would exceed **30%** → ⛔ BLOCKED
3. Ticker matches a **no-fly zone** → ⛔ BLOCKED
4. Leveraged ETF combined exposure would exceed **10%** → ⛔ BLOCKED

**Evaluate each warning condition:**

5. Single-stock position lands between **8–15%** → ⚠️ WARNING
6. Sector concentration lands between **25–30%** → ⚠️ WARNING
7. Overweight proposed but checklist passes **<3/5** items → ⚠️ WARNING
8. Leveraged ETF held **>180 days** without a trend review → ⚠️ WARNING

**Output rules:**

- If **any BLOCKED condition is true**: output the block below before the Sizing Signal. The Sizing Signal must be downgraded to At-weight or Underweight unless Jeff explicitly overrides in the chat.
- If **only WARNING conditions**: output a warning callout below the Sizing Signal.
- If **all clear**: output a single line: `✅ Risk Cap Gate — no violations.`

**BLOCKED output format:**
```
> ⛔ RISK CAP VIOLATION — [TICKER]
> **Blocked condition(s):**
> - [State which condition(s) triggered, with the specific numbers]
> **Required action:** Sizing Signal downgraded to [At-weight / Underweight].
> Override requires explicit confirmation in chat before proceeding.
```

**WARNING output format:**
```
> ⚠️ RISK CAP WARNING — [TICKER]
> **Warning condition(s):**
> - [State which condition(s) triggered, with the specific numbers]
> Sizing Signal is [signal] — review before sizing up.
```

---

## Strategy Alignment Check

*Based on `risk_rules.json` + `strategy_framework.md`. Detailed checklist — the Risk Cap Gate above handles hard stops; this section provides the full picture.*

| Check | Result | Notes |
|---|---|---|
| Single-stock cap (8% entry / 15% review) | ✅ / ⚠️ / ❌ | [Current % + projected at proposed size] |
| Sector concentration (25% alert / 30% limit) | ✅ / ⚠️ / ❌ | [Current sector % + projected after sizing] |
| No-fly zone | ✅ / ❌ | [Does this ticker qualify under no-fly rules?] |
| Leveraged ETF cap (10% combined) | ✅ / ⚠️ / N/A | [Only if leveraged ETF] |
| Screening checklist | X/5 passed | [List which items pass/fail — ETFs auto-pass items 1 & 3] |
| Account fit | ✅ / ⚠️ | [Does proposed account match account-specific strategy?] |
| Cooling-off window | ✅ / ⚠️ / N/A | [Was this ticker sold within the last 30 days in a taxable account?] |

**Overall alignment:** [Aligned / Partial / Misaligned]
[1 sentence: if Partial or Misaligned, state specifically what conflicts with strategy and what would need to change.]

---

## Smart Money [STOCKS ONLY — omit for ETFs/funds]

Query SQLite for which tracked investors hold this ticker in their most recent 13F:
```python
import sqlite3
from pathlib import Path
conn = sqlite3.connect(Path.home() / "Claude/investing-tool/investing.db")
conn.row_factory = sqlite3.Row
sm_rows = conn.execute(
    """SELECT h.quarter, h.value_k, h.pct_portfolio, h.shares, i.name AS investor_name
       FROM sm_holdings h JOIN sm_investors i ON h.investor_id = i.id
       WHERE (h.ticker = ? OR h.issuer_name LIKE ?)
         AND h.quarter = (SELECT MAX(h2.quarter) FROM sm_holdings h2
                          WHERE h2.investor_id = h.investor_id)
       ORDER BY h.value_k DESC""",
    (TICKER, f"%{TICKER}%")
).fetchall()
conn.close()
```

**If holdings found**, output this table:

| Investor | Quarter | Value | % of Portfolio | Shares |
|---|---|---|---|---|
| [name] | [quarter] | [$XB/$XM] | [X.XX%] | [X,XXX,XXX] |

**Conviction signal** (1 sentence below the table):
- 4+ investors → "★ High cross-portfolio conviction ([N] of [total] tracked investors)"
- 2–3 investors → "◈ Moderate conviction ([N] tracked investors)"
- 1 investor → "◇ Single tracked investor — [investor name] at [X%] of their portfolio"
- 0 → "No tracked investors hold [TICKER] in the most recent 13F data on file."

**If no data at all in SQLite**: "No smart money data loaded yet — run quarterly 13F update: `python3 ~/Claude/investing-tool/smart_money.py fetch`"

*Note: 13F data lags up to 45 days after quarter-end. Long equity only — no shorts or options.*

---

## Tax Overlay [include ONLY when Sizing Signal = Underweight AND account is known]

*Decision support only — not tax advice. Consult CPA before executing.*

Query `tax_events` in SQLite for any active entries for this ticker:
```python
import sqlite3
from pathlib import Path
conn = sqlite3.connect(Path.home() / "Claude/investing-tool/investing.db")
conn.row_factory = sqlite3.Row
tax_rows = conn.execute(
    "SELECT * FROM tax_events WHERE ticker = ? AND status = 'active' ORDER BY event_date DESC LIMIT 5",
    (TICKER,)
).fetchall()
wash_open = conn.execute(
    """SELECT * FROM tax_events WHERE ticker = ? AND event_type IN ('wash_sale','sell_decision')
       AND wash_sale_until >= date('now') ORDER BY event_date DESC LIMIT 1""",
    (TICKER,)
).fetchone()
conn.close()
```

Surface the following in the output (omit rows that don't apply):

| Tax Factor | Value | Flag |
|---|---|---|
| Account type | [Taxable / Traditional IRA / Roth IRA] | [See framing below] |
| Days held | [X days] | STCG / LTCG |
| Days until LTCG threshold | [X days, if short-term] | ⚠️ if <60 days away |
| Unrealized gain/loss | [$X] | ▲ gain or ▼ loss |
| Wash-sale window | [open until DATE] or "none" | ⚠️ if open |
| Prior TLH events logged | [count] | [link to tax list] |

**Account framing (one sentence):**
- **Taxable:** State whether STCG or LTCG applies; if STCG and <60 days from threshold, flag whether waiting makes sense given the thesis.
- **Traditional IRA:** Note no LTCG benefit; sell freely on thesis.
- **Roth IRA:** Flag if selling a winner sacrifices tax-free compounding; only recommend if thesis is genuinely broken.

**If a wash-sale window is open:** Show the re-entry blackout date prominently. Warn against buying back within 30 days.

**To log this sell decision:** `python3 ~/Claude/investing-tool/journal.py tax sell [TICKER]`

---

## Personal Thesis

[3–5 sentence opinionated takeaway. State clearly: what is the core bet, what would make you add more, what would make you sell. No hedged conclusions like "there are risks but also opportunities." Pick a side.]
```

---

### Step 8a — Macro Specificity Validator (run before writing to Notion)

After drafting the Macro Context section, self-audit it against this checklist:

- [ ] Mentions the ticker by name or pronoun referring to it (not just "the market" / "growth stocks")
- [ ] Cites at least one specific FRED indicator with current value (FEDFUNDS, CPI YoY, 10Y, UNRATE, Dollar Index, GDP, M2 — pulled from Step 3's `--macro` snapshot)
- [ ] Connects the indicator to a specific business mechanism: revenue line, gross/net margin, valuation multiple, FCF yield, or input costs

**Fail patterns — if you see any of these in your draft, REWRITE the Macro Context before continuing:**
- "Rising rates are bad" / "Falling rates are good" without ticker-specific impact
- "Macro headwinds" / "macro tailwinds" without naming the indicator and value
- "AI demand is strong" / "consumer spending is weak" without a FRED citation
- "Recession risk" without a specific FRED trigger (UNRATE rising, yield curve, etc.)
- Any sentence that could apply to any ticker in the same sector unchanged

If the macro snapshot lacks an obvious connection to this company (rare — but possible for, e.g., a defensive utility in a calm rate environment), state that explicitly: "Current FRED indicators (FEDFUNDS X%, CPI X%) are neutral for TICKER given [specific reason — e.g., regulated utility with cost-of-capital pass-through]. No material macro tilt this quarter."

### Step 9 — Write to Notion

**Find or create the Analysis Page:**
1. Search the Analysis Pages DB (data_source_id: `a4f8265c-4e03-4169-af32-8822b24f5cf7`) for an existing page with the ticker name.
2. If found: use `notion-update-page` with `update_content` to **prepend** a new dated section at the top (keep prior analyses below so thesis evolution is visible).
3. If not found: use `notion-create-pages` with parent `data_source_id: a4f8265c-4e03-4169-af32-8822b24f5cf7` to create a new page titled `[TICKER] Analysis`.

**Update the Portfolio DB entry:**
Use `notion-update-page` on the Portfolio DB entry (look up page ID from `~/Claude/investing-tool/notion_page_ids.json`) to set:
- `date:Last Analyzed:start` = today's date
- `Sizing Signal` = the signal from the analysis
- `Conviction Rank` = numeric rank if assigned (leave blank if unscored)

---

## Important Constraints

- **SeekingAlpha access**: SA.com is blocked via Chrome MCP and WebFetch. SA ratings come from `sa_ratings.json` only. SA article analysis only happens when the user manually pastes article text into the chat.
- **yfinance quirk**: ETF YTD returns may show anomalous values (e.g., 600%+) — flag and ignore if clearly wrong; use 3yr/5yr returns instead.
- **Alpha Vantage limit**: 25 req/day — skip AV cross-check if the daily limit has been reached; note this in the output.
- **Macro data**: CPI is calculated as YoY% from FRED CPIAUCSL (13 months of data). Always express as YoY%, not the raw index level.
- **Conviction Rank is relative**: Never assign an absolute score. Instead, compare the holding to similar positions in the portfolio and state relative conviction (e.g., "higher conviction than CRWD, lower than MSFT").
- **ETFs vs Stocks**: Use the ETF output template for any Type = ETF, ETF-Leveraged, or Mutual Fund. The CEO section must be omitted. The ETF/Fund Profile section replaces it.

---

## After Completion

Tell the user:
- The Notion Analysis Page URL (or that it was updated)
- Which Portfolio DB field was updated (Last Analyzed, Sizing Signal)
- Whether SA article analysis was included or skipped
- Any data quality flags (AV discrepancy, missing SA ratings, ETF data quirks)
