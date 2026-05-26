# CONTEXT.md Format

CONTEXT.md is a glossary. It has exactly one job: define the shared language of this domain so that any agent, developer, or domain expert uses the same words to mean the same things.

## What belongs in CONTEXT.md

- **Terms** — nouns that name things in this domain
- **Anti-patterns** — how a term or concept gets misused, and why that matters
- **Relationships** — how entities relate to each other (one-to-many, exclusive, etc.)
- **Boundaries** — what a term explicitly does *not* include

## What does NOT belong in CONTEXT.md

- Project goals, timelines, or status updates
- Implementation details (field names, data types, SQL schemas)
- Architectural decisions (those go in `docs/adr/`)
- Behavioral instructions for Claude (those go in `CLAUDE.md`)
- In-progress work or handoff state (that goes in `HANDOFF.md`)

---

## Format

```markdown
# [Project Name] — Context

> Last updated: YYYY-MM-DD

## [Entity or Concept Name]

[One or two sentences defining the term precisely. Avoid jargon. Write for a domain expert who doesn't know the code.]

**Is not:** [What this term explicitly excludes, if non-obvious]

**Anti-pattern:** [How this term gets misused, and why it matters — high-signal entry]

**Relates to:** [Other terms in this glossary it connects to]

---

## [Next Term]

...
```

## Example entry

```markdown
## Standalone Video

A video that exists independently of any course or lesson structure. Identified by a null `lesson_id`.

**Is not:** A video that happens to be short, or a video that could theoretically be part of a course but isn't yet.

**Anti-pattern:** Calling any short-form video a "standalone video." Only videos with no lesson association are standalone. Conflating the two breaks the UI filter logic and confuses pitch attribution.

**Relates to:** Pitch, Lesson, Course
```

## Updating CONTEXT.md

Update it inline as decisions are made — do not batch updates to the end of a session. The agent that just resolved the term is the best author for documenting it.

Add a `Last updated` timestamp at the top each time you edit.
