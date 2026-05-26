# ADR Format

An Architectural Decision Record (ADR) documents a single hard-to-reverse decision. Create one only when all three criteria are met:

1. **Hard to reverse** — changing your mind later has meaningful cost
2. **Surprising without context** — a future reader would wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives, and you picked one for specific reasons

If any of the three is missing, skip the ADR.

---

## File naming

`docs/adr/NNNN-short-description.md`

Start at `0001`. Increment sequentially. Use kebab-case. Examples:
- `0001-single-consolidated-gitlab-role.md`
- `0002-lessons-learned-in-docs-not-memory.md`

---

## Format

```markdown
# ADR-NNNN: [Short title — what was decided]

**Date:** YYYY-MM-DD  
**Status:** Accepted | Superseded by ADR-NNNN

---

## Context

[What situation forced this decision? What constraints existed? What was ambiguous or at risk?
2–4 sentences. Do not editorialize — just state the facts of the situation.]

## Decision

[What was decided, stated clearly and directly. One paragraph maximum.]

## Alternatives considered

| Option | Why rejected |
|--------|-------------|
| [Alternative 1] | [Specific reason] |
| [Alternative 2] | [Specific reason] |

## Consequences

**Positive:** [What this decision enables or simplifies]

**Negative / trade-offs:** [What this decision forecloses or complicates]

**Do not re-litigate:** [The specific thing a future agent or developer should not re-open without a very good reason]
```

---

## Example

```markdown
# ADR-0001: GitLab presented as a single consolidated role

**Date:** 2026-05-17  
**Status:** Accepted

---

## Context

Jeff held three successive titles at GitLab (Senior Director → Director → Senior Manager). Resume reviewers and recruiters may interpret separate role entries as shorter tenures or lateral moves, which weakens the narrative of progressive leadership.

## Decision

GitLab is presented as a single consolidated role on the resume and in all job search materials. The title shown is the highest held (Senior Director). Tenure spans the full period across all three titles.

## Alternatives considered

| Option | Why rejected |
|--------|-------------|
| List all three titles separately | Creates impression of 3 short roles; invites questions about demotions |
| List only the most recent title | Understates scope and seniority of full tenure |

## Consequences

**Positive:** Presents a clean, senior narrative. Tenure length is clear and credible.

**Negative / trade-offs:** Requires a brief verbal explanation if interviewer asks about title progression.

**Do not re-litigate:** Do not split the GitLab role into separate entries without explicit approval. The consolidated framing is locked.
```
