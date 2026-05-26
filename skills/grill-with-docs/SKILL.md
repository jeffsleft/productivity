---
name: grill-with-docs
description: "Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, HANDOFF.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a plan against their project's language and documented decisions. When there is a codebase, cross-references code. When there is no codebase, adapts to ops/strategy/personal project posture."
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions in batches — I like a long list so I can go on a walk and transcribe my thoughts in a narrative. If a question can be answered by reading existing files, read them instead of asking me.

## Step 1: Orient before grilling

Before asking anything, do the following:

1. **Detect project type.** Look for source code files (`.py`, `.js`, `.ts`, `.go`, etc.). If found: code project — enable code cross-referencing. If not found: ops/strategy/personal project — skip code cross-referencing, adopt analytical/outcome-oriented posture.

2. **Load the context stack.** Look for `CONTEXT.md` files using a priority-ordered stack: start in the current directory, walk up toward the project root. Load all that exist. The most local one takes precedence on any term conflict.

3. **Load HANDOFF.md if it exists.** Read it to understand locked decisions, in-progress work, and what's already been resolved. Do not re-litigate anything marked as locked or decided.

4. **Load CLAUDE.md if it exists.** Understand project-specific behavioral constraints before asking anything that might conflict with them.

5. **Check for ADRs.** Look for a `docs/adr/` directory. If it exists, scan for ADRs relevant to the plan being discussed.

6. **Report what you found** in one short paragraph before the first question: what context was loaded, what locked decisions exist, and any immediate glossary conflicts with what the user has described.

## Step 2: The grilling session

### Adapt posture to project type

**Code projects:** Ask about architecture, data model, API surface, conventions, and edge cases. Cross-reference with code. Surface contradictions between what the user says and what the code does.

**Ops/strategy/personal projects:** Ask about outcomes, decision-making criteria, stakeholders, constraints, and edge cases. No code cross-referencing. Think like a Chief of Staff or strategy analyst — probe assumptions, surface dependencies, pressure-test the logic.

### Challenge against the glossary

When the user uses a term that conflicts with an existing entry in `CONTEXT.md`, call it out immediately:
> "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Prioritise anti-patterns

When capturing terms in `CONTEXT.md`, anti-pattern entries carry more weight than positive guidelines. "Never do Y because of Z" is higher signal than "use X." Proactively ask: "Is there a way this term or concept gets misused or misunderstood that we should document?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term:
> "You're saying 'account' — do you mean the Customer or the Organisation? Those are different things in this domain."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent edge cases that force precision about boundaries between concepts.

### Cross-reference with code (code projects only)

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it:
> "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Do not batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

**CONTEXT.md is a glossary. Nothing else.**
- No project goals, timelines, or status
- No implementation details or specs
- No architectural decisions (those go in ADRs)
- Only terms that are meaningful to a domain expert who doesn't know the code

Create `CONTEXT.md` lazily — only when the first term is resolved. If one already exists, update it in place.

### Offer ADRs sparingly

Only offer to create an ADR when **all three** are true:
1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

Create `docs/adr/` lazily — only when the first ADR is needed.

## Step 3: Session close

When the grilling session is complete or the user signals they're done:

1. **Summarise resolved decisions** — one line each, with where they were captured (CONTEXT.md entry, ADR number, or CLAUDE.md).

2. **Prompt HANDOFF.md update.** Ask: "Do you want me to update HANDOFF.md with what's been resolved, what's now in progress, and what's next?" If yes, update it. The HANDOFF.md should reflect the current state a cold-start agent would need to pick up the work — not a history log.

3. **Flag any open questions** that weren't resolved and should become Todoist tasks.
