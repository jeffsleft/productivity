# journal skill

## Purpose
Handles investment decision journaling — trades, beliefs, and road-not-taken entries. Writes to SQLite (`investing.db`) and to the Notion Trade & Belief Journal DB.

---

## When to invoke
Trigger on any of: "I bought", "I sold", "I trimmed", "I added to", "I passed on", "I decided not to buy/sell", "I believe", "log this trade", "record this decision", "journal", "not buying", "holding despite".

---

## Entry Types

| User says… | Notion Entry Type | SQLite decision_type |
|---|---|---|
| "I bought X" | Trade-Buy | `bought` |
| "I sold X" | Trade-Sell | `sold` |
| "I added to X" / "I bought more" | Trade-Add | `partial_buy` |
| "I trimmed X" / "I reduced" | Trade-Trim | `partial_sell` |
| "I decided not to buy X" | Not-Bought | `not_bought` |
| "I decided not to sell X" | Not-Sold | `not_sold` |
| "I believe [macro/sector thesis]" | Belief | `belief` |
| "I'm watching X" (no action) | Watchlist | `watchlist` |
| Quick skip, no research | Passed | `passed` |

**`not_bought` vs `passed`**: Use `not_bought` when you researched seriously and made a deliberate decision. Use `passed` for a quick skip. `not_bought` triggers the full checklist + pre-mortem flow.

---

## Required Fields by Entry Type

**All entries:**
- Ticker (use "MACRO" for market-wide beliefs with no specific ticker)
- Date (default today)
- Reasoning / thesis

**Trade entries** (`bought`, `sold`, `partial_buy`, `partial_sell`, `not_bought`, `not_sold`):
- Price at entry
- Account: Taxable / Traditional IRA / Roth IRA

**Buy-side entries** (`bought`, `partial_buy`, `not_bought`):
- Bull Case: "What would have to be true for this to succeed?"
- Bear Case / Risks: "What would have to be true for this to fail?"
- Expected Hold Period
- Exit Conditions: "What price target, fundamental trigger, or time horizon would cause you to exit?"
- Goal Bucket: Retirement / College — [name] / Legacy / N/A

**Belief entries:**
- Macro Beliefs: the specific claim being made
- Themes/sectors affected (put in Reasoning)
- Account if relevant

---

## Workflow

### Step 1: Detect entry type
Parse the user's message. Confirm if ambiguous.

### Step 2: Collect fields conversationally
Ask for missing fields one at a time. Don't dump all questions at once. Reference the required fields table above.

### Step 3: Pre-Buy Thesis Gate (buy entries + not_bought)
**Mandatory — do not skip.** After collecting the bull and bear cases, ask:

> "One more check — if you look back in 2 years and this thesis failed, what's the single most likely reason? What's the bear case that worries you most?"

Store the answer in `pre_mortem`. This must be answered before confirming. If the user already gave a strong bear case, push further: "Is there anything more specific you're worried about?"

### Step 4: 5-Item Checklist (bought + partial_buy only)
Walk through each item and record pass/fail:
1. SA Quant Rating ≥ 3.5
2. Revenue growth positive YoY
3. FCF positive
4. Not in over-concentrated sector (>30%)
5. Thesis aligns with 3+ year conviction horizon

Warn if fewer than 3 pass: "⚠ Only N/5 checklist items passed — note this in your reasoning."

### Step 5: Confirm
Show a structured summary of all collected fields. Ask "Save this entry?"

### Step 6: Write to SQLite
Run via Bash:
```bash
cd /Users/jeffbeaumont/Claude/investing-tool && python3 -c "
import json, sys
sys.path.insert(0, '.')
from journal import get_conn, init_db
init_db()
with get_conn() as conn:
    cur = conn.execute('''
        INSERT INTO investment_notes
            (ticker, date, decision_type, price, thesis, macro_context,
             concerns, confidence, prediction, prediction_horizon,
             account, checklist_flags, pre_mortem, goal_bucket)
        VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
    ''', (
        TICKER, DATE, DECISION_TYPE, PRICE, THESIS,
        MACRO_OR_NONE, CONCERNS_OR_NONE, CONFIDENCE_OR_NONE,
        PREDICTION_OR_NONE, HORIZON_OR_NONE,
        ACCOUNT_OR_NONE, CHECKLIST_JSON_OR_NONE,
        PRE_MORTEM_OR_NONE, GOAL_BUCKET_OR_NONE
    ))
    print(f'Saved note #{cur.lastrowid}')
"
```
Capture the note ID for the Notion write.

### Step 7: Write to Notion
Create a page in the Trade & Belief Journal DB:
- **DB ID**: `c40af2a18a5a42c9bcacb23c3c94f9f3`
- **Parent**: 📈 Investment Research (`33ff0f47-cb6c-818e-a756-e329c2f30370`)

Page title format: `[TICKER] — [Entry Type] — [YYYY-MM-DD]`

Set all properties matching the DB schema. Map SQLite fields to Notion properties:

| SQLite field | Notion property |
|---|---|
| `decision_type` mapped | Entry Type (use Notion label, e.g. `bought` → "Trade-Buy") |
| `ticker` | Ticker |
| `date` | Date |
| `price` | Price at Entry |
| `account` | Account |
| `thesis` | Reasoning |
| `macro_context` | Macro Beliefs |
| `concerns` | Bear Case / Risks |
| `checklist_flags` (format as readable text) | Checklist Flags |
| `pre_mortem` | Pre-Mortem |
| `goal_bucket` | Goal Bucket |
| SQLite note id | SQLite ID |

For `checklist_flags`, render as readable text in Notion (not raw JSON):
```
✓ SA Quant Rating ≥ 3.5
✗ Revenue growth positive YoY
...
3/5 passed
```

### Step 8: Confirm to user
Report: "Logged entry #[sqlite_id] for [TICKER] — [entry type] on [date]. [Notion link]"
If checklist < 3: append the warning.

---

## Decision Type → Notion Entry Type mapping

| SQLite | Notion |
|---|---|
| bought | Trade-Buy |
| sold | Trade-Sell |
| partial_buy | Trade-Add |
| partial_sell | Trade-Trim |
| not_bought | Not-Bought |
| not_sold | Not-Sold |
| belief | Belief |
| watchlist | Watchlist |
| passed | Passed |

---

## Notes
- The `journal.py` CLI uses interactive input — don't call it interactively. Use the inline Python approach in Step 6, or call `journal.get_conn()` and `journal.init_db()` directly.
- Always verify the SQLite write succeeded before writing to Notion.
- If Notion MCP is unavailable, complete the SQLite write and tell the user to sync Notion manually later.
- `belief` entries with ticker = "MACRO" are valid — they represent market-wide or macro views that aren't tied to a specific holding.
