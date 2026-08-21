---
name: tax-advisor
description: Fire when considering tax-loss harvesting, selling positions, logging 529 contributions, managing wash-sale windows, or optimizing account location
version: 1.0.0
---

# /tax-advisor

## Trigger
User types `/tax-advisor` or asks about:
- "Should I harvest a loss on X?"
- "What's the tax impact of selling X?"
- "Log a 529 contribution"
- "Wash-sale check on X"
- "Asset location — what should go in which account?"
- "Show me my tax events"

---

## What This Skill Does
V2-C tax layer: account-aware sell framing, TLH surfacing, wash-sale tracking, 529 contribution tracking, and asset-location guidance. This is **decision support only** — always flag to consult CPA before acting.

All data is logged to SQLite (`investing.db` → `tax_events` table) and Notion ("Tax Events" DB, data source: `[YOUR_TAX_EVENTS_DS_ID]`).

---

## Tax Logic by Account Type

| Account | Sell Bias | Key Rules |
|---|---|---|
| **Taxable** | Tax-sensitive | Prefer LTCG (>366 days); harvest losses; watch wash-sale window; check if approaching 1yr threshold before selling |
| **Traditional IRA** | Thesis-driven | Tax-deferred — sell freely on thesis; no LTCG benefit; no wash-sale rule applies in IRA |
| **Roth IRA** | Maximum growth | Tax-free compounding — never sell a winner unless thesis is broken; highest conviction only |
| **529** | Education-specific | Track contributions per kid vs $150K target; note annual gift-tax exclusion (~$19K/donor in 2026) |

---

## Subcommand Flows

### 1. TLH Candidate (`python3 journal.py tax tlh`)

**When to invoke:** User mentions an underwater position in a taxable account, or asks about harvesting.

**Steps:**
1. Run: `python3 [YOUR_INVESTING_TOOL_PATH]/journal.py tax tlh`
2. Walk the prompts (ticker, account, purchase date, cost basis, current price, shares)
3. The CLI will compute:
   - Days held → STCG vs LTCG classification
   - Unrealized loss amount
   - Earliest safe re-buy date (wash-sale window)
4. After logging, prompt: "Do you want me to write this to Notion as well?"
5. If yes → create a page in the Tax Events Notion DB

**TLH decision support to include in chat output:**
- Is the loss worth harvesting? (STCG losses more valuable — offset ordinary income)
- What's the replacement candidate? (similar exposure, different security — avoids wash-sale)
- Is the position within 30 days of crossing the LTCG threshold? If so, waiting may be better.
- Is there a realized gain elsewhere this year to offset?

---

### 2. Wash-Sale Warning (`python3 journal.py tax wash TICKER`)

**When to invoke:** User sold a position and may want to re-enter, or just sold for a loss.

**Steps:**
1. Run: `python3 [YOUR_INVESTING_TOOL_PATH]/journal.py tax wash TICKER`
2. Walk prompts (sell date, account, sale price, cost basis)
3. CLI shows: safe re-entry date = sell date + 30 days
4. Sync to Notion if user confirms

**Key IRS wash-sale rules to surface:**
- Applies 30 days BEFORE and AFTER the sale (not just after)
- Applies to "substantially identical" securities — ETFs tracking the same index may qualify
- Does NOT apply inside IRA or Roth (but you can't claim the loss in those accounts anyway)
- Disallowed loss is added to cost basis of the replacement shares (deferred, not lost)

---

### 3. 529 Contribution (`python3 journal.py tax 529`)

**When to invoke:** User mentions making a 529 contribution, or asks about college savings progress.

**Steps:**
1. Run: `python3 [YOUR_INVESTING_TOOL_PATH]/journal.py tax 529`
2. CLI shows current YTD totals for all three kids and prompts for the new contribution
3. After logging, show progress toward each kid's $150K target
4. Sync to Notion if user confirms

**529 context to surface:**
- Annual gift-tax exclusion 2026: ~$19,000/donor/beneficiary
- Superfunding (5-year election): up to ~$95,000 lump sum per beneficiary
- College timelines: Lauren → Sep 2029, Samuel → Sep 2031, Sadie → Sep 2034
- Flag if YTD total is approaching the annual exclusion limit

---

### 4. Sell Decision (`python3 journal.py tax sell TICKER`)

**When to invoke:** User is considering selling a position and wants the full tax framing before deciding.

**Steps:**
1. Run: `python3 [YOUR_INVESTING_TOOL_PATH]/journal.py tax sell TICKER`
2. Walk prompts (account, purchase date, cost basis, current/proposed price, shares)
3. CLI computes and surfaces:
   - Days held → STCG vs LTCG
   - Days until LTCG threshold (if short-term)
   - Total gain/loss
   - Wash-sale window if selling for a loss
   - Account-specific framing (Taxable/IRA/Roth)
4. Sync to Notion if user confirms

---

### 5. List Tax Events (`python3 journal.py tax list [--days N]`)

**When to invoke:** User asks "what tax events have I logged?" or "show my wash-sale windows."

**Steps:**
1. Run: `python3 [YOUR_INVESTING_TOOL_PATH]/journal.py tax list --days 90`
2. Output lists all events grouped by type
3. CLI auto-highlights any open wash-sale windows and 529 YTD summary
4. No Notion write needed — read-only display

---

### 6. Asset-Location Guidance (conversational — no CLI command)

**When to invoke:** User asks which account type is best for a specific holding.

**General rules to apply:**

| Asset Type | Preferred Account | Reasoning |
|---|---|---|
| High-growth individual stocks | Roth IRA | Tax-free compounding on gains |
| Dividend-paying stocks / REITs | Traditional IRA | Dividends taxed as ordinary income in taxable; deferred in IRA |
| Tax-efficient index ETFs (e.g., VTI, VOO) | Taxable | Low turnover = low taxable events; LTCG rates apply |
| Bonds / fixed income | Traditional IRA | Interest income taxed as ordinary income; deferred in IRA |
| Leveraged ETFs | Taxable or IRA | High turnover = frequent taxable events if in taxable |
| International funds | Taxable | Foreign tax credit only available in taxable accounts |
| Money market / cash | Any | Low yield; tax drag is minimal |

Surface these rules when the user asks, and cross-reference with the Account field on existing Portfolio DB entries.

---

## Notion Write (Tax Events DB)

**DB data source:** `[YOUR_TAX_EVENTS_DS_ID]`

When writing a tax event to Notion, create a page with these mapped fields:

| Notion Field | Source |
|---|---|
| Name | `[TICKER] — [Event Type] — [Date]` (e.g., "GTLB — TLH Candidate — 2026-04-21") |
| Event Type | tlh_candidate → "TLH Candidate", wash_sale → "Wash Sale", contribution_529 → "529 Contribution", sell_decision → "Sell Decision" |
| Ticker | ticker |
| Account | account |
| Event Date | event_date |
| Cost Basis | cost_basis |
| Current Price | current_price |
| Shares | shares |
| Unrealized G/L | unrealized_gl |
| Days Held | days_held |
| Gain Type | gain_type |
| Wash Sale Until | wash_sale_until |
| Kid | kid_name (for 529 only) |
| 529 Contribution | contribution (for 529 only) |
| YTD 529 Total | ytd_total (for 529 only) |
| Status | "Active" |
| Notes | notes |

---

## Disclaimer (always include in output)

> ⚠️ **Decision support only — not tax advice.** Tax laws are complex and individual circumstances vary. Always consult a CPA or tax advisor before executing any tax-motivated trade or contribution.
