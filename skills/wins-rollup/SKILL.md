---
name: wins-rollup
description: Monthly roll-up of wins across all active projects, graded by outcome quality. Collects docs/wins.md from every project, classifies each item as a Measured Outcome, Demonstrated Capability, or Activity, interrogates the strongest activity-only items for a missing metric, and produces a roll-up for review and Accomplishments Inventory promotion. Runs in Claude Code (local filesystem) or claude.ai (uploaded files / Notion source).
version: 2.0
---

# Wins Roll-Up Skill

## Trigger

User runs `/wins-rollup` — typically on the 1st of each month per the recurring Todoist task.

## Core principle: a win is a measurable outcome, not an activity

Most raw `wins.md` entries log **activity** ("shipped a deck," "added 14 indexes," "went public"). A resume and a hiring manager care about **outcomes**: what changed, by how much, against what baseline, toward what objective. This skill's job is to separate the two honestly — never to launder activity into "accomplishment," and never to fabricate a metric that doesn't exist. The skill can only classify what the source contains, so it also feeds better structure back into the source files for next month.

### The three outcome grades (use these labels verbatim)

- **✓ Measured** — has all four: an **objective** it serves, a **baseline** (before-state), a **result/delta** (measured change), and a **proof source**. Resume-safe.
- **⚠️ Demonstrated** — the system/feature is real and shippable, but no baseline→result metric exists yet. This is the honest, legitimate home for most personal-project wins. It is **not** a failure grade — it's a credible proof point, labeled truthfully.
- **✗ Activity** — built or shipped something with no outcome framing at all. Either a candidate for interrogation (Step 2.5) or it stays in the activity log only.

**Hard rule:** never promote a ⚠️ or ✗ as a ✓. When in doubt, grade down. Inflating an outcome is worse than admitting there isn't one yet.

---

## Step 1 — Collect (environment-aware)

**First determine the source, based on where the skill is running:**

**A. Claude Code / local filesystem available** — find and read every project's wins file:
```
find ~/Projects -name "wins.md" -path "*/docs/*" -not -path "*/.claude/worktrees/*" -not -path "*/node_modules/*"
```
Read each in full. Note the project name from the path.

**B. claude.ai (web/desktop) — no access to ~/Projects.** Do NOT run the `find` above; it will fail or return nothing. Instead:
1. Ask Jeff to attach the `docs/wins.md` files (or paste their contents), **or**
2. If a Notion "Wins" source exists, pull from it via the Notion connector.
If neither is available, say so plainly and stop — do not synthesize a roll-up from memory.

**Either way**, also locate the most recent prior roll-up to set the window (only synthesize what's new since its date):
- Local: `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/wins-rollup-*.md` (most recent).
- claude.ai: ask Jeff for the date of the last roll-up, or the prior file.

---

## Step 2 — Classify, then synthesize

**Before writing anything, grade every candidate item** against the three grades above. For each, note internally: objective / baseline / result / proof / grade. This grading drives Steps 2.5 and 3.

Then produce a roll-up with this structure:

### Header
> **Wins Roll-Up — [Month Year]**
> Generated: [date] | Window: since [prior roll-up date] | Sources: [projects]
> Active this window: [list]. Quiet: [list].

### Section 1 — What Shipped
Full activity log. Nothing is lost here — every meaningful item, activity included, gets a bullet. Be specific: metrics, names, proof points where they exist. This is the complete record; the grading is applied in Section 4, not here.

### Section 2 — Themes and Patterns
2–4 sentences on what the wins have in common. What kind of builder does this body of work reveal? What capability shows up repeatedly?

### Section 3 — Standout Moments
3–5 individual wins strongest for career storytelling — hard technical problem, real operational need, or memorable "so what."

### Section 4 — Candidates for Accomplishments Inventory (split by grade)
This is the graded section. Split into two subsections and never merge them:

**✓ Measured Outcomes** *(resume-safe — objective + baseline + result + proof)*
> **[Project/context]** — [action] [what], moving [metric] from [baseline] to [result]. Proof: [source].

**⚠️ Demonstrated Capability** *(real proof point; outcome data pending — label honestly)*
> **[Project/context]** — [action] [what]. Objective: [what it demonstrates]. Proof: [live URL / repo / deployed confirmation]. *(No baseline metric yet.)*

Close Section 4 with a one-line honesty note: *"[N] items shipped this window remain Activity-only — logged in Section 1, not yet outcome-graded."*

---

## Step 2.5 — Interrogate for missing metrics (this is where real outcomes enter the pipeline)

Before finalizing, pick the **top 3–5 ✗ Activity items that plausibly HAVE a real metric behind them** but didn't capture it. Present them to Jeff and ask, for each:

> "**[Item]** — is there a baseline→result number here? (e.g. before/after latency, count, adoption, time saved.) If yes, give me the delta and I'll promote it to ✓ Measured. If not, confirm and I'll keep it as ⚠️ Demonstrated."

Apply his answers. This step is the whole point of the redesign: measurable outcomes get **supplied by Jeff at roll-up time**, not invented after the fact by the synthesizer. Do not guess numbers to fill the gap.

---

## Step 3 — Output

Present the roll-up in chat. Ask: "Anything to add, correct, or promote to the Accomplishments Inventory before I save this?"

Wait for response. Apply edits. Then ask where to save:
1. Notion (under the "Wins Roll-Ups" page/database Jeff specifies)
2. `.docx` to `[YOUR_PROJECTS_PATH]/Professional Development/_Resume & Portfolio/`
3. Continue the `.md` series in `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/`
4. Some combination

Save to the chosen destination(s). Confirm with file path or Notion URL.

---

## Step 4 — Feed the source (closes the loop for next month)

The roll-up is only as good as the raw `wins.md` entries. After saving, **offer** (do not do automatically):

1. For each item Jeff supplied a metric for in Step 2.5, append the baseline/result/proof back into that project's `docs/wins.md` so the outcome is captured at source — next month it grades ✓ automatically.
2. For any project whose `wins.md` is thin on outcome structure, offer to add the entry template below to the top of the file as a reminder.

### Wins entry template (the shape every future entry should aim for)
```markdown
## [Feature/Fix Name] — [YYYY-MM-DD]
- **Objective:** [what career/capability/business outcome this serves]
- **Baseline:** [before-state, with a number if one exists]
- **Result:** [after-state / measured delta]
- **Proof:** [logs, benchmark, deployed URL, repo, user data — how we know]
```
Not every entry can be ✓ Measured, and that's fine — an entry with objective + result + proof but no baseline metric is a legitimate ⚠️ Demonstrated. The template just makes the missing fields visible instead of silently absent.

---

## Notes

- **Read-only on wins.md during Steps 1–3.** The only writes to `wins.md` happen in Step 4, with Jeff's approval.
- **First run:** synthesize all wins across all time. Subsequent runs focus on what's new since the last roll-up date.
- **New projects with no `docs/wins.md`:** note as a gap at the end — don't create the file automatically.
- **claude.ai note:** this skill must be added to the cloud account via claude.ai → Settings → Skills to appear there; a local Claude Code copy does not auto-sync to the cloud. See the "Skill Platform Architecture" section of the workspace CLAUDE.md.
