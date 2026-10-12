---
name: decision-accountability
description: >
  Reads the CoS Decision Log file and surfaces open Todoist tasks or project files that contradict a logged decision. Also appends new decisions observed during a session. Run standalone or as part of the Friday CoS review. Use when Jeff says "check the decision log", "decision accountability", "what decisions are we tracking", "log a decision", or when called by the weekly-review or cos-sync skills.
version: 2.0
---

# Decision Accountability
### Version 2.0 — August 2026
### Part of: CoS Plugin
### Platform: Claude Cowork / Claude Code + local filesystem + Todoist MCP

---

## What This Skill Does

**One job: catch the case where Jeff decided something and the system kept acting as if he hadn't.**

That happens in two directions:
1. An open Todoist task, project file, or load plan **contradicts** a logged decision.
2. A CoS session **re-raises** a question the log already closed.

Both are failures of the same kind, and both waste Jeff's attention. Direction 2 is the more common one and the more annoying one.

The skill also **appends** decisions observed during a session, so the log stays current without Jeff having to maintain it.

---

## ⚠️ WHAT THIS SKILL NO LONGER DOES — read before running

**v1.0 counted staleness and reported it.** "The log is 38 days stale." Then 46. Then 53. It did this for four consecutive sessions and produced zero value, because:

- The Apple Note it was reading **could not be resolved by name and probably never existed.** The v1.0 constants table listed the folder as `TBD`. The flags were unverifiable.
- Even if it had existed, **silence is not a defect.** A week with no decisions logged means no decisions were made. That is normal.

**Rules now:**
- ❌ Do NOT report "the log is N days stale" or any variant.
- ❌ Do NOT flag decisions as "due for follow-up" on a 14-day timer.
- ❌ Do NOT flag decisions as "silently abandoned" on a 30-day timer.
- ✅ DO report contradictions, in both directions.
- ✅ DO append new decisions.
- ✅ If the log has nothing to flag, say one line and move on.

---

## SOURCE OF TRUTH

**Path:** `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/decision-log.md`

Read it with `Read`. Append to it with `Edit`. No MCP, no paste step, no overwrite risk. **This is a plain file and it is directly writable — do not ask Jeff to paste anything into it.**

Established 2026-08-28, replacing an Apple Note that was never confirmed to exist.

---

## Decision Log Format

Newest section on top, grouped by month. Each entry:

```markdown
### YYYY-MM-DD — [Decision title]
**Decision:** [What was decided, one or two sentences]
**Why:** [Reasoning or forcing function]
**Decided by:** [Jeff / a review / a named source]
**Detail:** [File path or Todoist ID where the full context lives]
```

Optional lines, only when they carry weight:
- `**Standing constraints:**` — rules that flow from the decision
- `**Scope note:**` — what the decision does NOT cover
- `**Open conflict:**` — a known contradiction not yet resolved

Keep entries short. The log is an index of closed questions, not a narrative.

---

## HOW TO RUN

### Step 1 — Read the log

Read the whole file. It is short by design. Build a working list of every decision and what it closes.

### Step 2 — Check direction 1: does anything contradict a decision?

Pull open Todoist tasks (all projects). Also scan the current load plan and any project files in scope for the session.

Look for:
- A task whose **date, framing, or description** contradicts a logged decision. *Example caught 2026-08-28: "Figure out trees for on the hill" carried an 8/20 date against a logged Sept/Oct deferral.*
- A task description that states a **blocker the log has already resolved**. *Example: an offsite task named a logistics issue as the blocker after Jeff had said the blocker was the job outcome.*
- A project file or sheet row **still describing a question the log has closed**.

### Step 3 — Check direction 2: is this session about to re-raise a closed question?

Before any other CoS skill proposes a question, a coaching point, or a "still open" item, check it against the log.

**A decision in the log closes the question. Do not re-litigate it, do not re-flag it, do not ask about it "one more time to be sure."**

This check exists because the failure is documented and repeated:
- Vercel commute / anchor days — closed 2026-08-14, raised again afterward. Jeff: *"stop asking, this is dumb."*
- Self-ruleout language coaching — closed 2026-08-26, raised again 2026-08-28. Jeff: *"I don't know what you're talking about."*

If a stale register, an old walk script, or a prior report still lists a closed item as open, **the log supersedes it**. Say so and move on.

### Step 4 — Append decisions from this session

Any decision Jeff made during the session goes into the log before session close. Do not ask permission to log a decision he plainly made; write it and show him the entry.

Write it in his terms, not yours. Quote him when the wording matters.

### Step 5 — Report

```
DECISION LOG — [DATE]

CONTRADICTIONS
- [Task / file]: "[name]"
  Contradicts: [Decision title] ([date])
  → Proposed fix: [paste-ready replacement text or the specific Todoist edit]

CLOSED — do not re-raise
[Only list these when another part of the session was about to raise one.]
- [Decision title] ([date]) — [what tried to re-raise it]

LOGGED THIS SESSION
- [YYYY-MM-DD] [Decision title]
```

If nothing to flag: **"Decision log clean — no contradictions."** One line. Nothing else.

**Always draft the fix, never just the flag.** A contradiction reported without paste-ready replacement text gets re-flagged next session and nothing changes. This is a standing rule across the CoS plugin.

---

## LOGGING A DECISION MID-SESSION

> "Log a decision: [text]"

Fill in Why / Decided by / Detail from session context. Only ask Jeff if a field genuinely cannot be inferred, and ask for all missing fields in **one** block. Then append and show the entry.

Sixty seconds, no ceremony.

---

## INTEGRATION WITH FRIDAY REVIEW

When called from `weekly-review` or `cos-sync`:
- Run Steps 1–4 silently.
- **Run Step 3 before the review authors any questions or coaching points.** It is a gate on the rest of the session, not a section at the end.
- Surface only contradictions and newly logged decisions. No counts, no health summary, no "N decisions tracked."
- If there is nothing, contribute nothing. A clean log should add zero lines to the review.

---

## KEY CONSTANTS

| Constant | Value |
|----------|-------|
| Decision log path | `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/decision-log.md` |
| Read method | `Read` tool, direct |
| Write method | `Edit` tool, direct — append under the current month |
| Staleness thresholds | **None. Removed in v2.0.** |

---

## TRIGGER PHRASES

- "Check the decision log"
- "Decision accountability"
- "What decisions are we tracking"
- "Log a decision: [text]"
- "Did I already decide this?"
- Called automatically by `weekly-review` or `cos-sync`

---

## GRACEFUL DEGRADATION

| Missing | Behavior |
|---------|---------|
| Log file doesn't exist | Create it with the header and format above. Do not report this as a defect; just build it. |
| Todoist MCP unavailable | Run Step 3 only (the re-raise gate). Skip Step 2. Note the gap in one line. |
| Running somewhere without filesystem access | Say the log is unreachable and skip the section entirely. Do NOT fall back to guessing at staleness from other signals — that is the exact failure v2.0 removes. |

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-03 | v1.0 — created. Read a `CoS — Decision Log` Apple Note; flagged overdue follow-ups, contradictions, and abandoned decisions. |
| 2026-08-28 | v2.0 — **Source moved from Apple Notes to `cos-plugin/decision-log.md`.** The v1.0 note was never confirmed to exist (constants table listed the folder as `TBD`), so four sessions of staleness flags were unverifiable. **Removed all staleness, follow-up-timer and abandonment reporting** per Jeff — silence means no decisions were made, which is normal. **Added Step 3, the re-raise gate**, which is now the skill's primary job: stop other CoS skills from re-opening questions the log has closed. Log is now directly readable and writable, so decisions get appended automatically instead of being handed to Jeff to paste. |
