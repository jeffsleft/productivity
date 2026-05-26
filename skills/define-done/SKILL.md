---
name: define-done
description: Initiates the Outcome Definition Process for a new project or initiative. Captures the user's goal statement, then hands off to outcome-architect to audit it against the Outcome Deployment Framework (ODF), ask clarifying questions, and produce an authorized Outcome Charter. Nothing goes to gemini-builder until outcome-architect issues a /goal authorization block. Trigger on "/define-done [text]", "let's define this project", "starting a new project", "define done for this", or any time a new initiative begins and no outcome_charter.md exists yet.
version: 1.0.0
---

# /define-done — Outcome Definition Process

Initiates the Outcome Definition Process for a new project or initiative. Captures the user's goal statement, hands off to outcome-architect to audit against the Outcome Deployment Framework (ODF), and produces an authorized Outcome Charter. Nothing goes to gemini-builder until outcome-architect issues a `/goal` authorization block.

---

## When to invoke
Trigger on: `/define-done [text]`, "let's define this project", "what are we solving for", "let's set the goal", "starting a new project", "define done for this", or any time a new initiative is about to begin and no `outcome_charter.md` exists yet.

---

## What /define-done takes as input

The user types: `/define-done [their goal statement]`

Example:
```
/define-done This project is done when we have a process capable of processing expense reports and categorizing every expense into the correct general ledger category.
```

If the user types `/define-done` with no text, prompt them:
> "What does 'done' look like for this project? Describe it in one sentence in business language — not technical terms."

---

## Step-by-Step Execution

### Step 1 — Capture the goal statement
Extract the goal text from the skill invocation args. If empty, ask the user to state it (see above).

### Step 2 — Check for existing charter
```bash
ls outcome_charter.md 2>/dev/null && echo "EXISTS" || echo "NONE"
```

If `outcome_charter.md` exists: read it and confirm with the user whether this is an update to an existing project or a new initiative.

### Step 3 — Write initial charter skeleton
Create `outcome_charter.md` in the current project root with the goal statement captured:

```markdown
# Outcome Charter

_Status: DRAFT — pending outcome-architect audit_
_Created: [TODAY'S DATE]_

## Initial Goal Statement (user-provided)

[GOAL TEXT FROM USER]

## Definition of Done

_To be refined by outcome-architect_

## Key Results

_To be defined by outcome-architect_

## Current State

_To be audited by outcome-architect_

## Authorization

_Pending — gemini-builder is BLOCKED until outcome-architect issues /goal block_
```

### Step 4 — Hand off to outcome-architect
Invoke the outcome-architect agent with the goal statement and the path to the draft charter:

> "outcome-architect: A new goal has been submitted. The initial goal statement is: '[GOAL TEXT]'. The draft outcome_charter.md has been created. Please run your ODF audit, ask any clarifying questions you need, and issue a /goal authorization block when ready."

### Step 5 — outcome-architect takes over
outcome-architect will:
1. Assess confidence (if <90%, ask the user clarifying questions)
2. Run the ODF audit via Gemini
3. Refine the outcome_charter.md
4. Issue the `/goal` authorization block when approved

### Step 6 — Confirm to user
Once outcome-architect issues the `/goal` block, confirm:
> "Outcome charter approved. outcome_charter.md written to [project root]. gemini-builder is authorized to proceed."

---

## Notes
- `/define-done` is the front door. outcome-architect owns the audit and the charter.
- gemini-builder must never be invoked until the `/goal` authorization block is present in the session or in `outcome_charter.md`.
- If the user is in the middle of an existing project and runs `/define-done` again, treat it as a charter update — read the existing charter first and diff against the new goal statement.
- The goal statement should be in business language ("expense reports are categorized correctly") not technical language ("the pipeline runs"). If the user gives a technical goal, flag it and ask for the business outcome it serves.
