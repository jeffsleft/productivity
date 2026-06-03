---
name: patterns
description: Fire when analyzing prediction accuracy by pattern tag, running calibration checks, or after each quarterly retrospective
version: 1.0.0
---

# /patterns

## Trigger
`/patterns` — run after any retrospective, or on demand to check calibration.

Optionally: `/patterns TAG` to drill into a specific pattern (e.g., `/patterns rate-sensitive`).

## What This Skill Does
Surfaces Jeff's historical prediction accuracy by pattern tag and confidence level. Answers:
- "When I make a high-confidence call, am I actually right more often?"
- "What types of decisions do I consistently get wrong?"
- "Am I overconfident or underconfident at specific conviction levels?"

Uses the `resolved_predictions` and `pattern_tags` SQLite tables populated by the retrospective workflow.

---

## Step-by-Step Execution

### Step 1 — Run the Patterns Report

```bash
cd ~/Claude/investing-tool && python3 journal.py patterns
```

For a specific tag:
```bash
cd ~/Claude/investing-tool && python3 -c "
import sqlite3, json
from pathlib import Path
conn = sqlite3.connect(Path.home() / 'Claude/investing-tool/investing.db')
conn.row_factory = sqlite3.Row
rows = conn.execute(
    '''SELECT r.* FROM resolved_predictions r
       WHERE json_extract(r.tags, '$') LIKE ?
       ORDER BY r.resolved_at DESC''',
    (f'%TAG%',)
).fetchall()
conn.close()
# Print results...
"
```

Or simply use the CLI:
```bash
python3 journal.py patterns
```

### Step 2 — Interpret and Synthesize

Read the output and add a 3–5 sentence synthesis covering:

1. **Overall hit rate** — is it above 60%? Below 50% is a systematic problem.
2. **Confidence calibration** — are high-confidence calls (4–5) actually more accurate than low (1–2)? If not, confidence scores are miscalibrated.
3. **Worst pattern** — which tag has the lowest hit rate? Is there a story behind it?
4. **Best pattern** — which tag is Jeff consistently good at? Lean into these.
5. **Recommendation** — 1–2 specific behavior changes based on the data.

### Step 3 — Tag Suggestions (if data is thin)

If fewer than 10 resolved predictions exist, note that calibration data is thin and encourage tagging more notes. Suggest common tags:

| Tag | When to use |
|-----|-------------|
| `growth-stock` | High-multiple, revenue-growth-driven thesis |
| `value-stock` | Thesis based on discount to intrinsic value |
| `rate-sensitive` | Call heavily influenced by interest rate direction |
| `ai-play` | AI/ML adoption or monetization thesis |
| `sector-rotation` | Bet on a sector re-rating relative to the market |
| `macro-call` | Belief entry about market-wide direction |
| `turnaround` | Thesis depends on operational or financial recovery |
| `momentum` | Following price or fundamental momentum |
| `defensive` | Thesis based on stability / recession resistance |
| `international` | Non-US exposure as a key element |
| `leveraged` | Leveraged ETF or amplified position |
| `concentration-risk` | Decision involving outsized position size |

---

## Integration with Retrospective

At the end of each retrospective, for every thesis scored:

1. **Tag the note** (if not already tagged):
   ```bash
   python3 journal.py tags NOTE_ID rate-sensitive growth-stock
   ```

2. **Resolve the prediction**:
   ```bash
   python3 journal.py resolve NOTE_ID --grade B
   ```

This feeds the calibration database. After 2–3 quarters of retrospectives, the pattern data becomes meaningful.

---

## Integration with Journal (Inline Pattern Stats)

When Jeff adds a new journal entry and mentions tags verbally (e.g., "this is a rate-sensitive bet"), look up the hit rate for that tag and surface it:

```bash
cd ~/Claude/investing-tool && python3 -c "
import sqlite3, json
from pathlib import Path
conn = sqlite3.connect(Path.home() / 'Claude/investing-tool/investing.db')
conn.row_factory = sqlite3.Row
rows = conn.execute(
    \"SELECT outcome_correct FROM resolved_predictions WHERE tags LIKE ?\",
    ('%rate-sensitive%',)
).fetchall()
conn.close()
if rows:
    n = len(rows)
    c = sum(r['outcome_correct'] for r in rows)
    print(f'rate-sensitive: {c}/{n} ({c/n*100:.0f}% hit rate)')
"
```

If a pattern has a hit rate below 50%, surface a calibration warning:
> ⚠️ Pattern note: your 'rate-sensitive' calls have been correct X% of the time (X of Y resolved). Consider adjusting confidence downward.

If above 70%:
> ✓ Pattern strength: your 'rate-sensitive' calls have been correct X% of the time — a consistent edge.

Only surface this if there are ≥3 resolved calls for that tag — too few is noise.

---

## Brier Score Reference

The Brier score measures calibration quality: `(probability - outcome)²`

Confidence levels map to these probabilities:
| Confidence | Label | Probability used |
|---|---|---|
| 1 | Low | 35% |
| 2 | Low-Med | 50% |
| 3 | Medium | 65% |
| 4 | Med-High | 80% |
| 5 | High | 90% |

Outcome: 1 = correct (grade A or B), 0 = incorrect (grade C, D, or F)

Score interpretation:
- **0.00** = Perfect (called it right at 100% confidence)
- **0.25** = Coin flip (no better than random)
- **> 0.25** = Worse than random — overconfident on wrong calls

A good investor targets avg Brier < 0.15 over time.

---

## Recommended Cadence

- Run after each quarterly retrospective (after resolving that quarter's grades)
- Run ad hoc if noticing a pattern of misses
- Minimum data for meaningful insight: 10+ resolved predictions

```bash
cd ~/Claude/investing-tool
python3 journal.py patterns           # Full calibration report
python3 journal.py tags --list        # See all tags in use
python3 journal.py tags NOTE_ID       # View tags on a specific note
python3 journal.py resolve NOTE_ID --grade B   # Log a grade
```
