---
name: smart-money
description: Fire when looking up smart money investor holdings, viewing who owns a ticker, updating 13F data, or managing smart money investor tracking
version: 1.0.0
---

# /smart-money

## Trigger
User types `/smart-money` or asks about:
- "Who owns [TICKER]?" / "What do smart money investors hold?"
- "Update the 13F data" / "Fetch the latest 13F"
- "Show me Buffett's / Klarman's / Ackman's holdings"
- "Import a 13F file"
- "Verify CIKs" / "Which CIKs need verification?"

---

## What This Skill Does
V2-I: Surfaces 13F-HR holdings from a curated list of tracked smart-money investors.
Data is stored in SQLite (`sm_investors` + `sm_holdings`) and summarized in the Notion "Smart Money Holdings" DB.

All commands run from `~/Claude/investing-tool/`.

---

## Tracked Investors

| Name | Entity | CIK | Verified? |
|---|---|---|---|
| Buffett / Berkshire | BERKSHIRE HATHAWAY INC | 1067983 | ✓ |
| Klarman / Baupost | BAUPOST GROUP LLC | 1061768 | needs verify |
| Ackman / Pershing Square | PERSHING SQUARE CAPITAL MANAGEMENT LP | 1336528 | needs verify |
| Akre Capital | AKRE CAPITAL MANAGEMENT LLC | 1112520 | needs verify |
| Polen Capital | POLEN CAPITAL MANAGEMENT LLC | 943090 | needs verify |
| Ruane Cunniff | RUANE CUNNIFF & GOLDFARB LLC | (none) | needs lookup |
| Tepper / Appaloosa | APPALOOSA LP | 1418814 | needs verify |
| Loeb / Third Point | THIRD POINT LLC | 1040273 | needs verify |
| Druckenmiller / Duquesne | DUQUESNE FAMILY OFFICE LLC | 1536411 | needs verify |
| Burry / Scion | SCION ASSET MANAGEMENT LLC | 1649339 | needs verify |

Jeff edits `investors.json` to add/remove investors, then re-runs `seed`.

---

## Quarterly Update Workflow

13F filings are due within 45 days of quarter-end:
- Q4 (Dec 31) → due mid-February
- Q1 (Mar 31) → due mid-May
- Q2 (Jun 30) → due mid-August
- Q3 (Sep 30) → due mid-November

### Auto-fetch (try first)
```bash
python3 ~/Claude/investing-tool/smart_money.py fetch
```
If auto-fetch is blocked (403 from EDGAR Archives), use the manual workflow below.

### Manual import workflow
For each investor that fails auto-fetch:
1. Go to the EDGAR URL printed by the fetch failure message
2. Click the latest 13F-HR filing
3. Find the "Information Table" document → click the XML link → Save As
4. Run:
```bash
python3 ~/Claude/investing-tool/smart_money.py import ~/Downloads/infotable.xml "Investor Name" 2025-Q4
```
Repeat for each investor.

### Verify CIKs first (one-time, before first fetch)
```bash
python3 ~/Claude/investing-tool/smart_money.py verify "Klarman / Baupost"
python3 ~/Claude/investing-tool/smart_money.py verify "Ackman / Pershing Square"
# ... etc for each unverified investor
# For Ruane Cunniff (no CIK):
python3 ~/Claude/investing-tool/smart_money.py lookup "Ruane Cunniff"
```

---

## Common Commands

```bash
# List all investors + fetch status
python3 ~/Claude/investing-tool/smart_money.py list

# See who holds a ticker (most recent quarter per investor)
python3 ~/Claude/investing-tool/smart_money.py view AAPL

# See all holdings for one investor (top 25 by value)
python3 ~/Claude/investing-tool/smart_money.py investor "Buffett / Berkshire"

# Find a CIK by entity name
python3 ~/Claude/investing-tool/smart_money.py lookup "Baupost"

# Verify a CIK is valid and has 13F filings
python3 ~/Claude/investing-tool/smart_money.py verify "Klarman / Baupost"

# Re-seed investors table from investors.json (after editing)
python3 ~/Claude/investing-tool/smart_money.py seed
```

---

## Notion Sync

After importing new holdings, sync to Notion "Smart Money Holdings" DB
(data source: `ec48c9e9-9354-4825-b8c8-a346c1750588`).

For each new holding row in SQLite, create a Notion page with:

| Notion Field | SQLite Source |
|---|---|
| Name | `[TICKER] — [Investor] — [Quarter]` |
| Investor | investor name (must match select option) |
| Ticker | ticker (may be null if unresolved) |
| Quarter | quarter (e.g. "2025-Q4") |
| Value | value_k × 1000 |
| Shares | shares |
| % Portfolio | pct_portfolio |
| Issuer Name | issuer_name |
| CUSIP | cusip |
| Conviction Count | run `python3 smart_money.py view TICKER` to get count |
| Last Updated | today's date |

Only sync the **top 30 positions by value** per investor per quarter to keep Notion lean.
Full history lives in SQLite.

---

## Surfacing in Analysis

When `/analyze-stock` runs on a ticker, the Smart Money section automatically queries SQLite:
```python
import sqlite3
from pathlib import Path
conn = sqlite3.connect(Path.home() / "Claude/investing-tool/investing.db")
conn.row_factory = sqlite3.Row
rows = conn.execute(
    """SELECT h.quarter, h.value_k, h.pct_portfolio, h.shares, i.name AS investor_name
       FROM sm_holdings h JOIN sm_investors i ON h.investor_id = i.id
       WHERE (h.ticker = ? OR h.issuer_name LIKE ?)
         AND h.quarter = (SELECT MAX(h2.quarter) FROM sm_holdings h2 WHERE h2.investor_id = h.investor_id)
       ORDER BY h.value_k DESC""",
    (TICKER, f"%{TICKER}%")
).fetchall()
conn.close()
```

If no data: note "No smart money data on file — run quarterly 13F update."

---

## Interpreting Results

**Conviction signals:**
- 4+ tracked investors hold the same ticker → high cross-portfolio conviction
- Single investor + >5% of their portfolio → high single-investor conviction
- New position (not in prior quarter) → recent buy signal
- Position reduced >50% → partial exit signal

**What this is NOT:**
- Real-time data — 13F filings lag by up to 45 days after quarter-end
- A complete picture — 13F only shows long equity positions ≥$200K; no shorts, options (by value), or non-US securities
- A buy signal on its own — use as one signal among many in analyze-stock synthesis
