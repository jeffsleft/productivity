# HANDOFF — Rules-layer refactor (agent-decomposition tune-up)

**Created:** 2026-06-02 by Opus 4.8 (planning session)
**Execute with:** Sonnet 4.6 (single session, Parts 1→2→3). See "Model note" at bottom.
**Full plan:** `~/.claude/plans/i-want-your-help-floofy-leaf.md` — read it first; this file adds execution order + gotchas.

---

## Why this exists

Jeff watched Anthropic's `agent-decomposition` workshop. Verdict: the principles have merit, and the workshop's villain — a bloated, always-loaded ~400-line prompt of task-specific rules — describes Jeff's **global rules layer**, not his skills (which are already correct). Standing context today is ~618 lines loaded into *every* session:
- `~/Projects/AI_RULES.md` = 261 lines (loaded via `@import` in `~/.claude/CLAUDE.md`)
- `~/Projects/.claude/CLAUDE.md` = 357 lines (workspace)

Most of AI_RULES is coding policy (Modal/SQLite/Cloudflare/etc.) that's dead weight on CoS/blog/finance/family sessions. Fix = progressive disclosure: slim AI_RULES to an always-on core + a Domain Rule Index, push domain rules into on-demand skills.

Scope approved by Jeff: **Full tune-up** = Part 1 (rules split) + Part 2 (fix 12 dead flat skills) + Part 3 (lightweight eval).

---

## CORE DESIGN RULE — do not violate

Progressive disclosure has a failure mode: a rule that used to always load now only loads when its skill triggers. For *conventions* that's fine. For *guardrails* (e.g. "don't `modal deploy` from a worktree", "don't `--force` a secret") a missed trigger is a **silent safety regression**.

Mitigation = **core holds pointers, skills hold content**:
1. Each extracted skill bundles its conventions AND its domain guardrails together.
2. Core AI_RULES keeps a compact **Domain Rule Index** (~15-line table: "working with X → load `x-conventions`") so Claude knows the skill exists.
3. Every new skill's `description:` must name the trigger condition precisely — trigger reliability is the entire safety margin. Vague description = guardrail silently dies.
4. Per AI_RULES §0: a rule has exactly ONE home. After moving a section to a skill, AI_RULES must **reference** it, never restate it.

---

## Execution order

### Part 1 — Split `~/Projects/AI_RULES.md` (OUT OF REPO — authorized)

> ⚠️ `AI_RULES.md` is at `~/Projects/`, parent of this repo. Editing it is editing global config. Jeff authorized this. Per his safety rule, show the diff before saving.

**Keep in core (target ~90 lines, down from 261):**
- §0 Three-layer model
- **NEW: Domain Rule Index** (pointer table → the 9 skills below)
- §4 Documentation / project file structure / Outcome workflow
- §7 GitHub conventions (trim to commit format + private-by-default)
- §11 Platforms to avoid

**Extract each section → new `skills/<name>/SKILL.md` in THIS repo, then deploy (Part-deploy below):**

| New skill dir | From AI_RULES § | Must preserve (guardrails) |
|---|---|---|
| `gemini-api-conventions` | §1 | pacing 5s, backoff ladder, `limit:0` probe |
| `scraping-conventions` | §2 | ATS→Jina→Firecrawl→BS chain, curl_cffi CVE pin |
| `modal-conventions` | §3 | **worktree-deploy warning, `--force` secret warning, route-collision** |
| `cloudflare-stack-rules` | §5 | (name avoids collision w/ generic `cloudflare` plugin skill) |
| `database-conventions` | §6 | SQLite-vs-Supabase decision, WAL, additive migrations |
| `notion-conventions` | §8 | REST-not-SDK, 2000-char split |
| `python-style` | §9 | f-string/Jinja traps, syntax gate |
| `frontend-htmx-conventions` | §10 | HTMX 302 + partial-href traps |
| `agent-delegation` | §12 | Antigravity two-stage gate, quota failover |

Each `SKILL.md` needs YAML frontmatter: `name`, `description` (precise trigger), `version: 1.0.0`. Body = section content near-verbatim. **Do not summarize guardrails — copy them.**

### Part 2 — Convert 12 dead flat skills

All 12 are one investing-insights cluster, flat `.md` with `# /command` + `## Trigger` but no YAML frontmatter → not registerable per Jeff's rule.

`add-source, analyze-portfolio, analyze-stock, ceo-score, deploy-test, goal-status, journal, patterns, retrospective, smart-money, stress, tax-advisor`

For each: make `skills/<name>/SKILL.md`, lift `## Trigger` + `## What This Skill Does` into a real `description:` field, keep body, preserve command name (dir name = slash command). `deploy-test` description should scope to "after deploying the investing-insights Modal app."

**Do NOT delete the flat `.md` files until** the dir versions are written AND Jeff confirms (his safety rule: show before deleting).

### Part 3 — Lightweight eval

Create `skills/_evals/rules-refactor-eval.md`: ~12 scenarios, each = opening prompt + skill(s) that SHOULD fire + skill(s) that should NOT. Cover: each guardrail domain (Modal/DB/delegation must fire on their prompts) AND non-coding sessions (CoS/blog/finance must load NO coding skill — that's the context-pollution win). Run via existing `skill-creator` skill (supports skill evals) or manually in fresh sessions, recording fire/no-fire.

**Eval must sample across all project types in `~/Projects/`** — not just CoS/blog. Three required scenario categories:
- **Code-heavy session** (`recruiting-engine`, `synapse-agent`, `investing-tool`, `voice-engine`, `Shipping container tracking`) — at least one scenario where the relevant coding skills fire correctly
- **Writing/ops session** (`Professional Development`, `Mercy Ships Finance`, `Home & Family`) — at least one scenario where zero coding skills load
- **Mixed session** (analysis on a code project, no deploy intent) — verifies only the actually-triggered subset loads, not the full coding suite

This validates the refactor works across the full workspace, not just the sessions it was designed in.

### Deploy + register

```
cp -r skills/<each-new-dir> ~/.claude/skills/
```
Then restart Claude Code so skills register. Confirm all 21 new dirs (9 convention + 12 investing) appear in the available-skills list.

---

## Verification (from plan)

1. `wc -l ~/Projects/AI_RULES.md` → ~90 (was 261).
2. Restart Claude Code; 9 convention + 12 investing skills appear in skill list.
3. Run eval scenarios: every guardrail skill fires on its domain prompt; no coding skill fires on a CoS/blog/finance prompt.
4. `grep -n "§3\|§5\|§6" ~/Projects/AI_RULES.md` → only Domain Rule Index pointers, no dangling refs. No rule lives in both core and a skill.
5. Smoke test: fresh session, "deploy to Modal from my worktree" → `modal-conventions` loads and surfaces the worktree warning.
6. Update [README.md](README.md) skills table with the new skills.

---

## ⚠️ TOP RISK — lossy extraction (review this explicitly)

The single biggest risk is **softening or dropping a guardrail** while moving a section from AI_RULES into a skill. Every guardrail in AI_RULES is scar tissue from a real incident (the file cites the dates). A line-count drop on AI_RULES (261→~90) is expected and fine; a **rule-count drop is a regression**.

**Mandatory check before marking Part 1 done:**
1. Before editing, list every guardrail/imperative in AI_RULES §§1,2,3,5,6,8,9,10,12 (each "Never…", "Always…", deploy-safety, secret-handling, CVE pin, trap).
2. After extraction, confirm each one appears verbatim in its destination skill. None summarized, none dropped.
3. Surface the before/after guardrail list to Jeff in the diff review — this is the one thing he wants to spot-check.

## Gotchas

- **Faithful extraction, not summarization.** Each guardrail in AI_RULES is there from a real incident (the file cites dates). Dropping one is a silent regression. Copy verbatim. (See TOP RISK above.)
- **Name `cloudflare-stack-rules`, not `cloudflare`** — a generic `cloudflare` plugin skill already exists; don't collide.
- **Two layers are out of repo:** `~/Projects/AI_RULES.md` and `~/.claude/skills/`. The repo's `skills/` is the source of truth; `~/.claude/skills/` is the deployed copy.
- **Don't touch** `~/Projects/.claude/CLAUDE.md` (357 lines) this pass. Plan flags 2–3 extractable sections there as a possible phase 2, but its profile/identity content stays. Leave it.
- **Show diffs before saving AI_RULES and before deleting any flat file** (Jeff's standing safety rule).

---

## Model note

Use **Sonnet 4.6** for all three parts in one session. Part 1's `description:` writing is the safety margin and needs judgment; Haiku risks compressing guardrails and writing weak triggers. Haiku is acceptable ONLY if peeling off Part 2 (pure mechanical flat→dir) as a separate pass. Do not re-engage Opus — no architecture decisions remain.

## Status — COMPLETE (2026-06-03, Sonnet 4.6 session)
- [x] Part 1 — AI_RULES split + Domain Rule Index + 9 convention skills
- [x] Part 2 — 12 flat→dir conversions
- [x] Part 3 — eval doc (`skills/_evals/rules-refactor-eval.md`)
- [x] Deploy to `~/.claude/skills/` + all 21 skills registered
- [x] Verified by gemini-researcher — all 4 checks PASS
- [ ] Delete flat .md files (pending Jeff confirmation — 12 investing .md files in `skills/` root)
