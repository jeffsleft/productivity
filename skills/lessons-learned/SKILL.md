---
name: lessons-learned
description: This skill should be used when the user signals the end of a coding session ("we're done", "let's wrap up", "session complete"), asks to document lessons learned, or wants to capture technical failures, fixes, and reusable patterns into the project's memory/lessons_learned.md file. Prevents future agents from re-introducing the same issues.
version: 1.0.0
---

# /lessons-learned — End-of-Session Lessons Learned Chronicler

Run this skill at the end of any coding session to capture failures, fixes, and reusable patterns into the project's `memory/lessons_learned.md`. Prevents future agents from re-introducing the same issues.

---

## When to run

- User says "we're done", "let's wrap up", "session complete", or explicitly asks for lessons learned.
- After any session where a bug was fixed, a technical decision was made, or a failure was diagnosed.
- Skip if the session was purely exploratory with no code changes.

---

## Step 1 — Locate the lessons file

Find `memory/lessons_learned.md` in the current project root. If it doesn't exist, create it with this header:

```markdown
# Lessons Learned

Per `AI_RULES.md` §4 — technical failures and their resolutions are logged here so future agents (Claude, Gemini CLI) don't re-introduce the same issues.

---
```

---

## Step 2 — Review the session

Scan the entire conversation for:
- Errors encountered (HTTP codes, stack traces, KeyErrors, 429s, crashes)
- Root causes identified
- Files changed and why
- Decisions made (architectural, model selection, library choice)
- Anything that surprised you or required multiple attempts
- Patterns or utilities worth reusing elsewhere

---

## Step 3 — Write the new entry

Prepend a new entry at the **top** of the file (after the header, before previous entries). Use this exact structure — omit sections only if genuinely not applicable:

```markdown
## YYYY-MM-DD — [Brief, descriptive title of the core issue or event]

### Symptom
Describe exactly how the failure manifested: UI messages, HTTP error codes, stack traces, log output. Use code blocks for raw error text.

### Root cause
Numbered or bulleted list of underlying causes. Be technically precise — name files, functions, line numbers, and environment variables. If there were overlapping causes, explain each one.

### Fix
What changed, organized by file. Include before/after code snippets where helpful. Be specific enough that a future agent can understand the change without reading the diff.

### Why all steps were needed
Explain why a partial fix would have failed. Cover the interplay between root causes.

### Verification
Steps to verify the fix: CLI commands, log flags to watch, UI flows to test.

### Postscript (if applicable)
Deeper technical realities discovered after the initial fix — undocumented API behavior, billing tier surprises, server-side resolution quirks, etc.

---

## Highlights, lowlights, epiphanies (session of YYYY-MM-DD)

### Highlights
- Bullet: what went well, fast deliveries, reusable diagnostics built.

### Lowlights
- Honest reflections on process mistakes — including AI mistakes (wrong exploration results, missed file checks, stale worktree deployments).

### Epiphanies
- Deep systemic realizations worth carrying into future sessions.

### Open follow-ups
- Technical debt, missing scripts, unseeded data, non-urgent features left over.

---

## Reusable patterns from this session (if any)

**Pattern A — [Name]**
Context: when to use it.
```python
# clean, generalized implementation
```
```

---

## Step 4 — Tone and style

- **Pragmatic and blunt.** Facts, mechanisms, file paths. No generic advice.
- **No fluff.** "Before deploying, confirm `pwd` is mainline, not a worktree" beats "deployment hygiene is important."
- **Hyper-scannable.** Bold key terms, code blocks for files/commands, tight bullet lists.
- **Include AI mistakes.** If an agent gave an inaccurate exploration result or deployed from a stale worktree, log it. Future agents need to know.

---

## Step 5 — Report back

After writing, tell the user:
1. File updated: `memory/lessons_learned.md`
2. One-sentence summary of what was logged
3. Offer to run `/lessons-learned-review` to check if anything should be elevated to `AI_RULES.md` or `CLAUDE.md`
