---
name: decision-accountability
description: >
  Reads the CoS Decision Log Apple Note and surfaces decisions that are overdue for follow-up, contradicted by open Todoist tasks, or silently abandoned. Run standalone or as part of the Friday CoS review. Use when Jeff says "check the decision log", "decision accountability", "what decisions are we tracking", or when called by the weekly-review or cos-sync skills.
version: 1.0
---

# Decision Accountability
### Version 1.0 — June 2026
### Part of: CoS Plugin
### Platform: Claude Cowork + Apple Notes MCP + Todoist MCP

---

## What This Skill Does

Closes the loop on decisions. Most decisions get made and then silently drift — either abandoned, reversed without acknowledgment, or blocked by open tasks that contradict them.

This skill does three things:
1. **Surfaces decisions due for follow-up** — any logged decision older than 2 weeks with no follow-up entry
2. **Flags contradictions** — open Todoist tasks that conflict with a logged decision
3. **Identifies abandoned decisions** — decisions made but never acted on, visible from task patterns

Runs standalone or hooks into the Friday CoS review as a standing section.

---

## Decision Log Format

The Decision Log lives in Apple Notes. Jeff confirmed the folder at setup — see `CoS — Decision Log` note.

Each entry follows this template:

```
[YYYY-MM-DD] — [Decision title]

Decision: [What was decided, in one sentence]
Why: [The reasoning or forcing function]
Tradeoff: [What was given up or accepted]
Reverses if: [What would cause this decision to be revisited]
Horizon: [Short-term / Medium-term / Long-term]
Follow-up: [Date + what to check — added after the fact]
```

Entries are newest-on-top. A `---` divider separates each entry.

---

## HOW TO RUN

### Step 1 — Read the Decision Log

Pull `CoS — Decision Log` from Apple Notes. Parse all entries. Extract:
- Decision title
- Date logged
- Horizon
- Whether a Follow-up line exists
- Whether a follow-up date is past due

### Step 2 — Pull open Todoist tasks

Pull Jeff's open tasks (all projects, limit 100). Look for:
- Tasks that appear to contradict a logged decision
- Tasks in projects that were specifically mentioned in a decision
- Tasks that are the expected "follow-through" action from a decision but don't exist

### Step 3 — Produce the accountability report

```
DECISION ACCOUNTABILITY — [DATE]

DECISIONS DUE FOR FOLLOW-UP
[Decisions logged more than 14 days ago with no Follow-up entry]
- [YYYY-MM-DD] [Decision title] — [X days since logged]
  Reverses if: [condition]
  → Prompt: Has this played out as expected? Add a follow-up note.

CONTRADICTIONS DETECTED
[Open Todoist tasks that appear to conflict with a logged decision]
- Task: "[Task name]" ([Project])
  Conflicts with: [Decision title] ([date])
  → Prompt: Is this task still valid, or should it be killed / updated?

SILENTLY ABANDONED
[Decisions with no corresponding Todoist task and no follow-up, older than 30 days]
- [YYYY-MM-DD] [Decision title]
  → Prompt: Did this get acted on? Mark it followed-up or log a reversal.

HEALTHY DECISIONS
[N] decisions logged, [N] with follow-ups, [N] still pending.
```

If nothing to flag: "Decision Log looks clean — no overdue follow-ups, no contradictions detected."

### Step 4 — Offer actions

For each flagged item, offer:
- "Add a follow-up note to this decision" → Jeff dictates; skill writes to the log
- "Kill the contradicting task" → confirm, then delete via Todoist MCP
- "Log a reversal" → Jeff explains why; skill adds a reversal entry to the log

---

## ADDING A NEW DECISION

Jeff can log a decision mid-session:
> "Log a decision: [decision text]"

Skill asks for the missing fields (Why, Tradeoff, Reverses if, Horizon) in one block, then prepends the new entry to the top of the Decision Log note.

Keep it fast — 60 seconds to log a decision, no ceremony.

---

## INTEGRATION WITH FRIDAY REVIEW

When called from `weekly-review` or `cos-sync`:
- Run Steps 1–3 silently
- Insert the accountability report as a standing section in the Friday review output
- Only surface items that require Jeff's attention — skip the "healthy decisions" count unless it's the only output

---

## THRESHOLDS

| Condition | Flag as |
|-----------|---------|
| Decision logged > 14 days, no follow-up | Due for follow-up |
| Decision logged > 30 days, no follow-up, no corresponding task | Silently abandoned |
| Open task appears to contradict a decision | Contradiction |
| Decision has a "Reverses if" condition that now appears true | Revisit prompt |

---

## KEY CONSTANTS

| Constant | Value |
|----------|-------|
| Decision Log note name | `CoS — Decision Log` |
| Apple Notes folder | TBD — confirmed at note creation (see `Create "CoS — Decision Log" Apple Note` Todoist task) |
| Follow-up window | 14 days |
| Abandoned threshold | 30 days |

---

## TRIGGER PHRASES

Run this skill when Jeff says any of:
- "Check the decision log"
- "Decision accountability"
- "What decisions are we tracking"
- "Any decisions I need to follow up on"
- "Log a decision: [text]"
- Called automatically by `weekly-review` or `cos-sync`

---

## GRACEFUL DEGRADATION

| Missing tool | Behavior |
|-------------|---------|
| Apple Notes MCP unavailable | Ask Jeff to paste the Decision Log content; run the analysis from the paste |
| Decision Log note doesn't exist yet | Prompt: "The Decision Log note hasn't been created yet. Want me to set it up now?" Then create it with the template and one seed entry |
| Todoist MCP unavailable | Run the log review only; skip contradiction detection; note the gap |

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-03 | v1.0 — created. Reads Decision Log Apple Note, surfaces overdue follow-ups, contradicting Todoist tasks, and silently abandoned decisions. Integrates with weekly-review and cos-sync as a standing section. |
