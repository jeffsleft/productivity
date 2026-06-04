---
name: lessons-learned-review
description: This skill should be used when the user wants to review accumulated lessons across projects and decide what should be promoted to AI_RULES.md, a domain skill, or CLAUDE.md as permanent rules. Run after /lessons-learned completes, when multiple projects have logged the same class of mistake, or periodically to prune stale rules and surface new ones. Always proposes changes and waits for approval before writing.
version: 1.1.0
---

# /lessons-learned-review — Elevate Lessons to Global Rules

Review accumulated lessons across all projects and decide what should be promoted to a permanent rule location. Proposes changes — never auto-applies.

**Architecture reminder:** `AI_RULES.md` is 93 lines and must stay lean. Domain-specific rules live in their skill (`modal-conventions`, `database-conventions`, etc.), not in AI_RULES. A lesson that would have gone to AI_RULES §3 (Modal) now goes to `modal-conventions/SKILL.md`. AI_RULES only grows when a lesson is truly universal — §0 meta-rules, §4 documentation standards, §7 GitHub conventions, §11 platforms to avoid.

---

## When to run

- After `/lessons-learned` completes and you want to check if anything should become a permanent rule.
- When multiple projects have logged the same class of mistake.
- Periodically (monthly or after a dense work period) to prune stale rules and surface new ones.

---

## Step 1 — Load the source files

Read all of the following:
- `~/Projects/AI_RULES.md` — core rules + Domain Rule Index (93 lines — check the index before routing anything here)
- `~/Projects/.claude/CLAUDE.md` — workspace-level preferences
- Every `memory/lessons_learned.md` found across `~/Projects/` (use glob or find)

For any lesson touching a known domain, also read the relevant skill:
- Modal → `~/.claude/skills/modal-conventions/SKILL.md`
- SQLite/Supabase → `~/.claude/skills/database-conventions/SKILL.md`
- Gemini API → `~/.claude/skills/gemini-api-conventions/SKILL.md`
- Cloudflare → `~/.claude/skills/cloudflare-stack-rules/SKILL.md`
- Web scraping → `~/.claude/skills/scraping-conventions/SKILL.md`
- Python → `~/.claude/skills/python-style/SKILL.md`
- HTMX/frontend → `~/.claude/skills/frontend-htmx-conventions/SKILL.md`
- Notion → `~/.claude/skills/notion-conventions/SKILL.md`
- Agent delegation → `~/.claude/skills/agent-delegation/SKILL.md`

---

## Step 2 — Identify promotion candidates

A lesson is worth promoting to a global rule when **any** of these are true:
- The same class of mistake has appeared in 2+ projects
- A root cause reveals an undocumented behavior of a tool, API, or platform Jeff uses universally
- A reusable pattern from one session would prevent a class of errors in all future projects
- A workflow mistake general enough to affect any project

A lesson is **not** worth promoting when:
- It's project-specific (a one-off bug in a single schema, for example)
- It's already documented in AI_RULES, a domain skill, or CLAUDE.md — check before proposing
- It's a symptom fix, not a root cause fix ("run this command" vs. "never do this pattern")

---

## Step 3 — Route each candidate to the right home

**This is the critical step.** Before proposing a change, ask: does a domain skill already own this?

| Target | When to use it |
|---|---|
| Domain skill (`modal-conventions`, `database-conventions`, etc.) | Lesson involves a specific platform or tool listed in the Domain Rule Index — **this is the default for most technical lessons** |
| `AI_RULES.md` §0 | Meta-rule about how the three-layer model works |
| `AI_RULES.md` §4 | Documentation standard, project file structure, outcome workflow |
| `AI_RULES.md` §7 | GitHub convention (commit format, repo naming, secrets) |
| `AI_RULES.md` §11 | Platform to avoid (Flask, VPS, heavy ORM) |
| `~/Projects/.claude/CLAUDE.md` | Jeff's personal workflow preference, standing permission, or proactive behavior |
| Project `CLAUDE.md` | Gotcha only relevant to one specific codebase |
| No action | Already covered, too narrow to generalize, or symptom-only |

**AI_RULES.md only grows if the rule is cross-domain and fits one of the four sections above.** If it fits a domain skill, it goes there — not AI_RULES. A Modal guardrail is not a "universal coding rule"; it's a Modal rule. One rule, one home.

---

## Step 4 — Draft proposed changes

For each promotion candidate, produce a proposed edit in this format:

```
### Proposed change [n]
Target: [domain-skill/SKILL.md] OR [AI_RULES.md §section] OR [CLAUDE.md section]
Type: New rule / Addition to existing rule / Correction

Current text (if modifying existing):
> [exact quote from the file]

Proposed text:
> [replacement or addition]

Rationale: [1-2 sentences — which lesson prompted this, why it's general enough to live here, and why AI_RULES is NOT the right home if routing to a skill]
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
- If a domain skill was updated, also deploy: `cp ~/Projects/productivity/skills/<name>/SKILL.md ~/.claude/skills/<name>/SKILL.md`
- Note what was elevated in `memory/rules_changelog.md` (create if missing) with: date, source lesson, target file, one-line summary of the change

---

## Style rules

**Writing into a domain skill:**
- Match the existing skill's voice — imperative, direct, technically precise
- Add under the most relevant existing section; don't create new top-level sections without reason
- Keep guardrails verbatim from the lesson — do not summarize them
- Lead with the rule, then the mechanism, then diagnostic/recovery steps

**Writing into `AI_RULES.md`:**
- Only §0, §4, §7, §11 are valid targets — if the rule doesn't fit one of these, it belongs in a skill
- Keep the core lean: a new entry in AI_RULES that could live in a skill is a drift violation
- Match existing section voice — imperative, no throat-clearing

**Writing into `CLAUDE.md`:**
- Keep it concise — read at session start for every project
- Proactive behavior permissions go under "Proactive Behavior Permissions"
- New skills or tools go in the "Connected Tools & MCP Inventory" table
