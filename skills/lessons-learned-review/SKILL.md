---
name: lessons-learned-review
description: This skill should be used when the user wants to review accumulated lessons across projects and decide what should be promoted to AI_RULES.md or CLAUDE.md as permanent universal rules. Run after /lessons-learned completes, when multiple projects have logged the same class of mistake, or periodically to prune stale rules and surface new ones. Always proposes changes and waits for approval before writing.
version: 1.0.0
---

# /lessons-learned-review — Elevate Lessons to Global Rules

Review accumulated lessons across all projects and decide what should be promoted to `AI_RULES.md` (universal coding rules) or `~/.claude/CLAUDE.md` (personal preferences and workflow). Proposes changes — never auto-applies.

---

## When to run

- After `/lessons-learned` completes and you want to check if anything should become a permanent rule.
- When multiple projects have logged the same class of mistake.
- Periodically (monthly or after a dense work period) to prune stale rules and surface new ones.

---

## Step 1 — Load the source files

Read all of the following:
- `~/Projects/AI_RULES.md` — universal coding rules (the highest-stakes file)
- `~/.claude/CLAUDE.md` — Jeff's personal profile and preferences
- `~/Projects/.claude/CLAUDE.md` — workspace-level preferences
- Every `memory/lessons_learned.md` found across `~/Projects/` (use glob or find)

---

## Step 2 — Identify promotion candidates

A lesson is worth promoting to a global rule when **any** of these are true:
- The same class of mistake has appeared in 2+ projects
- A root cause reveals an undocumented behavior of a tool, API, or platform Jeff uses universally (Modal, Gemini, Cloudflare, SQLite, GitHub)
- A reusable pattern from one session would prevent a class of errors in all future projects
- A workflow mistake (e.g., deploying from a worktree, hard dict access on config YAML) is general enough to affect any project

A lesson is **not** worth promoting when:
- It's project-specific (a one-off bug in a single schema, for example)
- It's already documented in `AI_RULES.md` or `CLAUDE.md` — check before proposing
- It's a symptom fix, not a root cause fix ("run this command" vs. "never do this pattern")

---

## Step 3 — Categorize each candidate

For each promotion candidate, classify it:

| Target file | When to use it |
|---|---|
| `AI_RULES.md` | Universal coding rule — applies to every project, every agent, every language |
| `~/.claude/CLAUDE.md` | Jeff's personal workflow preference or standing permission |
| Project `CLAUDE.md` | Project-specific gotcha — only relevant to one codebase |
| No action | Pattern is already covered, or too narrow to generalize |

---

## Step 4 — Draft proposed changes

For each promotion candidate, produce a proposed edit in this format:

```
### Proposed change [n]
Target: AI_RULES.md §[section] / CLAUDE.md / [project]/CLAUDE.md
Type: New rule / Addition to existing rule / Correction

Current text (if modifying existing):
> [exact quote from the file]

Proposed text:
> [replacement or addition]

Rationale: [1-2 sentences — which lesson prompted this, and why it's general enough to live here]
Source: [project/memory/lessons_learned.md, date]
```

---

## Step 5 — Present to Jeff for approval

List all proposed changes. For each one:
- Show the target file, the change, and the rationale
- Ask: **"Apply this change? [yes / no / modify]"**

Do **not** write to any file until Jeff approves. Apply approved changes one at a time, reading the current file state before each edit.

---

## Step 6 — After applying

For each applied change:
- Confirm the file was updated
- Note what was elevated in `memory/rules_changelog.md` (create if missing) with: date, source lesson, target file, one-line summary of the change

---

## Style rules for global rule additions

When writing new content for `AI_RULES.md`:
- Match the existing section's voice — imperative, direct, technically precise
- Lead with the rule itself, then the mechanism, then any diagnostic/recovery steps
- No "why this matters" throat-clearing — the rule body is the justification
- Reference the specific file/function/platform where relevant

When writing for `CLAUDE.md`:
- Keep it concise — this file is read at session start for every project
- Proactive behavior permissions and standing preferences go under the right section
- New skills or tools go in the "Connected Tools & MCP Inventory" table
