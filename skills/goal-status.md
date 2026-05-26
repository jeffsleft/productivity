# goal-status skill

## Purpose
Computes required CAGR for each financial goal, flags under-funded goals, and optionally links portfolio holdings to goals. Reads from the Notion Goals DB and the Portfolio DB.

---

## When to invoke
Trigger on: "How are my goals tracking?", "goal status", "am I on track for retirement?", "how much do I need to hit Lauren's college goal?", "goal check", or as part of the quarterly `retrospective` skill.

---

## Notion DBs
- **Goals DB ID:** `33647a5fe6f74d39b5ffe2f5ed3eb46c`
- **Goals Data Source:** `004455e9-3338-4517-a86f-db82408fb054`
- **Portfolio DB ID:** `61b7f3e7-0300-498d-ab47-cd36a413ed88`
- **Investment Research parent:** `33ff0f47-cb6c-818e-a756-e329c2f30370`

---

## Goals reference
| Goal | Target | Target Date |
|---|---|---|
| Retirement | $4,000,000 | 2044-01-01 |
| College — Lauren | $150,000 | 2029-09-01 |
| College — Samuel | $150,000 | 2031-09-01 |
| College — Sadie | $150,000 | 2034-09-01 |
| Legacy | No target | No date |

---

## Step-by-Step Execution

### Step 1 — Fetch goal rows from Notion
Use `notion-search` or `notion-fetch` on the Goals DB to get all rows. Extract: Goal name, Target Amount, Target Date, Current Value.

### Step 2 — Prompt for missing Current Values
If any goal has no Current Value set, ask the user:
> "To calculate required CAGR for [Goal], I need the current value you've allocated toward it. What's the current balance?"

For Retirement: this is the total of all retirement-earmarked accounts (Traditional IRA + Roth IRA combined, or whatever Jeff specifies).
For College goals: this is the balance of the relevant 529 or taxable savings set aside for that child.

If the user doesn't know, skip that goal and note it as "Current Value not set — CAGR not calculable."

### Step 3 — Compute Required CAGR
For each goal with both Current Value and Target Amount:

```
years_remaining = (target_date - today).days / 365.25
required_cagr = (target_amount / current_value) ^ (1 / years_remaining) - 1
```

Express as a percentage rounded to one decimal place.

**Interpretation thresholds:**
| Required CAGR | Status | Signal |
|---|---|---|
| ≤ 6% | On Track | Conservative enough that normal market returns cover it |
| 6–10% | Watch | Achievable but requires solid performance — monitor quarterly |
| 10–15% | Behind | Requires above-average returns — consider increasing contributions |
| > 15% | Behind | Likely underfunded — urgent review needed |

For Legacy (no target/date): status always "No Target" — skip CAGR calc.

### Step 4 — Update Notion Goals DB
For each goal computed:
- Set `Required CAGR %` to the computed value
- Set `Status` to the appropriate signal (On Track / Watch / Behind)
- Set `Current Value` if the user provided it
- Set `Last Updated` to today's date

### Step 5 — Output summary
Produce a clean table:

```
## Goal Status — [today's date]

| Goal | Target | Current | Required CAGR | Years Left | Status |
|---|---|---|---|---|---|
| Retirement | $4,000,000 | $X | X.X% | XX.X | ✅ On Track |
| College — Lauren | $150,000 | $X | X.X% | 3.4 | ⚠️ Watch |
| College — Samuel | $150,000 | $X | X.X% | 5.4 | ✅ On Track |
| College — Sadie | $150,000 | $X | X.X% | 8.4 | ✅ On Track |
| Legacy | — | — | — | — | No target |
```

Then a brief interpretation: which goals are the most urgent, what contribution increase would bring a "Behind" goal to "Watch" or "On Track."

### Step 6 — Flag urgent goals
If any goal is Behind (CAGR > 10%):
> "⚠️ [Goal] requires [X]% annual return to hit $[target] by [date]. At your current trajectory, you'd reach $[projected at 8%] by [date]. To close the gap at 8% return, you'd need to add $[monthly amount]/month in additional contributions."

Compute the monthly contribution needed to hit the target at 8% (the conservative end of Jeff's 8–12% target):
```
# PV = current_value, FV = target, r = 0.08/12 (monthly), n = months remaining
monthly_contrib = (FV - PV * (1 + r)^n) * r / ((1 + r)^n - 1)
```

### Step 7 — Portfolio-goal linkage (optional, if user asks)
If user asks "which holdings are funding [goal]?", cross-reference the Portfolio DB. Holdings tagged with a matching Goal Bucket (from V2-A journal entries) can be surfaced here. This is informational — no automated linkage required.

---

## Notes
- Lauren's college goal is the most time-constrained (~3.4 years). Flag this explicitly if behind.
- Required CAGR above 12% on any goal means it's outside Jeff's stated risk tolerance to achieve through returns alone — contribution increase is the primary lever.
- Always express CAGR as a real return (nominal target — inflation is not factored in here; keep it simple).
