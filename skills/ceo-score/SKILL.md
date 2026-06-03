---
name: ceo-score
description: Fire when scoring CEO/leadership quality, running scorecard on a ticker, evaluating CEO for investing analysis, or during analyze-stock when no current scorecard exists
version: 1.0.0
---

# ceo-score skill

## Purpose
Score the CEO/leadership quality for a given ticker using the V2-H scorecard methodology. Writes to SQLite (`ceo_scorecards` table) and the Notion CEO Scorecards DB.

---

## When to invoke
Trigger on: "score the CEO", "ceo score TICKER", "run a CEO scorecard on NVDA", "how good is [company]'s CEO?", "leadership quality check", or as part of a full `/analyze-stock` run when no current scorecard exists.

---

## Notion DB
- **CEO Scorecards DB ID:** `8a7dcf0bf8614b2ca440d392686fd378`
- **CEO Scorecards Data Source:** `4a4a31eb-5ece-4414-a4ea-2c1987d9c7ea`
- **Parent page:** `33ff0f47-cb6c-818e-a756-e329c2f30370` (📈 Investment Research)

---

## Scorecard Methodology (V2-H)

### Evidence base
- Bertrand & Schoar: CEO explains 5–10% of firm performance variance
- Kaplan CEO-trait research: execution + persistence > charisma
- Thorndike's *The Outsiders*: capital allocation is the dominant long-run driver
- Gompers/Ishii/Metrick + Bebchuk: governance signal strongest at mid-cap, weakest at mega-cap

### Composite score (100 pts) — weighted sum of 5 dimensions

| Dimension | Weight | Key criteria |
|---|---|---|
| Capital Allocation | 40% | Buyback timing vs price, M&A discipline/ROI, reinvestment quality, debt management |
| Execution & Reliability | 25% | Guidance accuracy, operational follow-through, re-org frequency as anti-signal |
| Talent & Culture | 20% | C-suite retention, Glassdoor trajectory, Lencioni/Collins lens, founder mentality, customer obsession, values consistency |
| Communication & Integrity | 10% | Shareholder letter quality, transparency on misses, narrative consistency, insider trading patterns |
| Board Quality | 5% | Board independence, tenure mix, committee strength, insider ownership, chair/CEO separation |

**Formula:** composite = (capital_allocation × 0.40) + (execution × 0.25) + (talent_culture × 0.20) + (communication × 0.10) + (board_quality × 0.05)

Score each dimension 0–100, then apply weights.

### Industry context — separate metric, NOT a score adjustment
Track as its own field: `industry_trajectory` (Rising / Stable / Declining / Mixed) + `industry_evidence` (one-line). This lets the reader understand the tailwind/headwind context without polluting the CEO composite.

### Tenure handling
Auto-tag based on years in role — no score penalty, just context:

| Tenure | Tag | Guidance |
|---|---|---|
| < 2 years | New — Thesis-based | Capital Allocation may be limited by short track record. Score on available evidence + prior company. New CEO = investable signal (e.g., Satya Nadella at MSFT turnaround). |
| 2–15 years | Established | Full scorecard valid. |
| > 15 years | Legacy — Change Risk | Full scorecard valid. Flag succession risk + potential calcification in Talent & Culture. |

### Hard flags — cap composite at 60
If any present, composite cannot exceed 60:
- Accounting restatement in past 5 years
- SEC enforcement against CEO
- Non-arm's-length related-party transactions
- Compensation grossly misaligned (e.g., option repricing after decline)

### Soft flags — −3 pts each, max −12
- Unexplained CFO turnover in past 2 years
- Pattern of missed guidance (>2 misses in 8 quarters)
- Chair = CEO without lead independent director
- Concentrated insider selling at highs without disclosed plan

---

## Step-by-Step Execution

### Step 1 — Check for existing scorecard
Search the Notion CEO Scorecards DB for an entry matching the ticker. If one exists and was scored within the last 180 days, show the user and ask: "Score already exists from [date] (composite: X/100). Update it?"

### Step 2 — Research phase (WebSearch)
Run two searches in parallel:
1. `"[TICKER]" CEO name tenure 2025 2026` — confirm CEO name and tenure start date
2. `"[TICKER]" CEO capital allocation M&A buybacks leadership Glassdoor 2025 2026` — gather evidence for scoring

Pull specific evidence for each dimension:
- **Capital Allocation**: buyback history (was it well-timed?), M&A track record, ROIC trend
- **Execution**: guidance beat/miss history, stated goals vs delivery
- **Talent & Culture**: exec turnover, Glassdoor score/trend, culture signals
- **Communication**: earnings call tone on misses, shareholder letter quality, consistency
- **Board Quality**: board composition, independence %, chair structure

### Step 3 — Present findings to user before scoring
Show a brief summary of what you found for each dimension, then ask: "Based on this research, shall I propose scores or would you like to score each dimension yourself?"

If user wants proposed scores: suggest a score for each dimension with 1-sentence justification. User can accept or override each.

If user wants to score themselves: present each dimension with criteria and wait for input.

### Step 4 — Score each dimension
For each dimension, land on a 0–100 score:
- 0–30: Poor / significant red flags
- 31–50: Below average
- 51–70: Average / adequate
- 71–85: Above average / strong
- 86–100: Exceptional / best-in-class evidence

### Step 5 — Capture industry context
Ask: "What's the industry trajectory for [sector]?" (Rising / Stable / Declining / Mixed) + one-line evidence.
This is informational — not factored into the composite.

### Step 6 — Check flags
Go through each hard flag and soft flag explicitly. For any flag that applies, note it.

### Step 7 — Compute composite
Apply the formula. Apply soft flag penalties. Apply hard flag cap if any present. Present final composite to user with breakdown.

### Step 8 — Write to SQLite
Run inline Python:
```python
import sqlite3, json
from pathlib import Path
DB_PATH = Path.home() / "Claude/investing-tool/investing.db"
conn = sqlite3.connect(DB_PATH)
conn.execute("""
    INSERT INTO ceo_scorecards (
        ticker, ceo_name, scored_at,
        tenure_years, tenure_tag,
        capital_allocation, execution, talent_culture,
        communication, board_quality,
        composite,
        industry_trajectory, industry_evidence,
        hard_flags, soft_flags,
        notes, sources
    ) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
""", (
    ticker, ceo_name, scored_at,
    tenure_years, tenure_tag,
    capital_allocation, execution, talent_culture,
    communication, board_quality,
    composite,
    industry_trajectory, industry_evidence,
    json.dumps(hard_flags),
    json.dumps(soft_flags),
    notes, sources
))
conn.commit()
conn.close()
```

### Step 9 — Write to Notion CEO Scorecards DB
Use `notion-create-pages` with `data_source_id: 4a4a31eb-5ece-4414-a4ea-2c1987d9c7ea`:

```
Ticker: [TICKER]
CEO Name: [ceo_name]
Scored At: [scored_at]
Tenure (Years): [tenure_years]
Tenure Tag: [one of: "New — Thesis-based" | "Established" | "Legacy — Change Risk"]
Capital Allocation (0–100): [score]
Execution (0–100): [score]
Talent & Culture (0–100): [score]
Communication & Integrity (0–100): [score]
Board Quality (0–100): [score]
Composite Score: [composite]
Industry Trajectory: [Rising | Stable | Declining | Mixed]
Industry Evidence: [one-line text]
Hard Flags: [multi-select from options]
Soft Flags: [multi-select from options]
Notes: [plain English summary]
Sources: [comma-separated URLs]
```

### Step 10 — Output summary
```
## CEO Scorecard — [TICKER]: [CEO Name]

| Dimension | Score | Weight | Contribution |
|---|---|---|---|
| Capital Allocation | XX/100 | 40% | XX pts |
| Execution & Reliability | XX/100 | 25% | XX pts |
| Talent & Culture | XX/100 | 20% | XX pts |
| Communication & Integrity | XX/100 | 10% | XX pts |
| Board Quality | XX/100 | 5% | XX pts |
| **Composite** | **XX/100** | — | — |

**Tenure:** X.X yrs — [New — Thesis-based / Established / Legacy — Change Risk]
**Industry Trajectory:** [Rising/Stable/Declining/Mixed] — [evidence]
**Hard Flags:** [list or "none"]
**Soft Flags:** [list or "none"]

[2–3 sentence plain English takeaway on this CEO's quality and what to watch]
```

---

## Output interpretation guidance

| Composite | Signal |
|---|---|
| 80–100 | Exceptional operator — a competitive advantage |
| 65–79 | Strong — above average with clear strengths |
| 50–64 | Adequate — not a headwind, not a tailwind |
| 35–49 | Concerning — specific weaknesses to watch |
| 0–34 | Red flag — CEO quality is a material risk factor |

---

## Notes
- Always surface the industry trajectory alongside the score so the context is clear.
- For New CEOs: note explicitly that the score has lower confidence. Flag it as a "thesis play" if relevant.
- For Legacy CEOs: flag succession question and whether the board has a plan.
- Board Quality carries only 5% weight — at mega-cap, this is intentionally low. Don't over-index on it.
- Capital Allocation at 40% is the dominant signal — prioritize research time accordingly.
