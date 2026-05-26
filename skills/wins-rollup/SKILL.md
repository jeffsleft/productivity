---
name: wins-rollup
description: Monthly roll-up of wins and celebrations across all active projects. Reads docs/wins.md from every project under ~/Projects/, synthesizes by theme, and produces a roll-up document for review and Accomplishments Inventory promotion.
version: 1.0
---

# Wins Roll-Up Skill

## Trigger

User runs `/wins-rollup` — typically on the 1st of each month per the recurring Todoist task.

---

## Step 1 — Collect

Find all `docs/wins.md` files under `~/Projects/`:

```
find ~/Projects -name "wins.md" -path "*/docs/*" -not -path "*/.claude/worktrees/*" -not -path "*/node_modules/*"
```

Read each file in full. Note the project name from the path.

---

## Step 2 — Synthesize

Produce a roll-up with this structure:

### Header
> **Wins Roll-Up — [Month Year]**
> Projects covered: [list]

### Section 1 — What Shipped
Bullet list of the most significant things built or completed since the last roll-up (or all-time if this is the first run). One bullet per meaningful item. Be specific — include metrics, names, and proof points where they exist in the source files.

### Section 2 — Themes and Patterns
2–4 sentences identifying what the wins have in common. What kind of builder does this body of work reveal? What capabilities are demonstrated repeatedly across projects?

### Section 3 — Standout Moments
3–5 individual wins that are strongest for career storytelling — either because they involved a hard technical problem, served a real operational need, or have a memorable "so what."

### Section 4 — Candidates for Accomplishments Inventory
Items from the wins that have metric-backed proof points or are strong resume-level accomplishments. These should be reviewed and considered for promotion to `~/Projects/Professional Development/_Resume & Portfolio/2026-05-16-Beaumont-Accomplishments-Inventory.docx`.

Format each as a draft bullet:
> **[Company/Project context]** — [action verb] [what was built/done], resulting in [outcome or proof point].

---

## Step 3 — Output

Present the roll-up in chat for Jeff to review.

Ask: "Anything to add, correct, or promote to the Accomplishments Inventory before I save this?"

Wait for response. Apply any edits.

Then ask where to save:
1. Save as a Notion page (under a "Wins Roll-Ups" database or page Jeff specifies)
2. Save as a `.docx` to `~/Projects/Professional Development/_Resume & Portfolio/`
3. Both

Save to the chosen destination(s). Confirm with file path or Notion URL.

---

## Notes

- **Do not auto-update wins.md files during this skill.** The roll-up is read-only. If Jeff wants to add something new to a project's wins.md, do that as a separate step after the roll-up.
- **First run:** If this is the first roll-up, synthesize all wins across all time. Subsequent runs should focus on what's new since the last roll-up date (check the most recent roll-up doc for the date).
- **New projects:** If a project under `~/Projects/` has no `docs/wins.md`, note it at the end of the roll-up as a gap — don't create the file automatically.
