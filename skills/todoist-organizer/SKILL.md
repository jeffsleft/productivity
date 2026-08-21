---
name: todoist-organizer
description: >
  Run a structured audit of Jeff's Todoist — clean dead tasks, flag vague ones, apply priority levels (P1–P4), assign AI leverage labels (🤖/🤝/👤), propose dates, detect duplicates, flag project moves, and produce a project health summary. Use this skill when Jeff says "organize my Todoist", "run the organizer", "let's clean up Todoist", "do a Todoist audit", "prioritize my tasks", or when the weekly-review skill needs a load plan. Runs after Walk & Talk Interviewer in the sequential CoS flow. Can also run standalone on any project or the full task list.
---

# Thoughtful Todoist Organizer
### Version 1.0 — May 2026
### Part of: CoS Plugin (Phase 2 of 3)
### Platform: Claude Cowork + Todoist MCP
### Follows: walk-and-talk-interviewer
### Precedes: execution-shadow (planned)

---

## What This Skill Does

An autonomous organizing pass over Jeff's Todoist. Unlike Walk & Talk (which extracts context by interviewing Jeff), the Organizer works from what's already in Todoist and makes its best judgment — then presents a full plan for Jeff's single confirm before writing anything.

**Core jobs, in order:**
1. Project health check — one-line status per project
2. Dead task detection — flag for kill, parking lot, or Won't Do
3. Duplicate detection — surface near-duplicates and propose merge or subtask
4. Vague task flagging — identify what needs clarification (for Walk & Talk handoff)
5. AI leverage labeling — 🤖/🤝/👤 on every non-trivial task
6. Priority assignment — P1–P4 applied across all tasks
7. Scheduling — assign start date and time to P1/P2 tasks
8. Project move proposals — flag tasks in the wrong home
9. Confirm + write — one shot after Jeff approves the full plan
10. Session report — saved to `cos-plugin/reports/`

**This skill is autonomous.** It reads, analyzes, proposes, and waits for one "go" before writing. It does not interview Jeff — that's Walk & Talk's job. If it hits tasks too ambiguous to classify, it flags them for Walk & Talk rather than guessing.

---

## Key Constants

| Constant | Value |
|----------|-------|
| Jeff's Todoist UID | `[YOUR_TODOIST_UID]` |
| [PARTNER]'s Todoist UID | `[PARTNER_TODOIST_UID]` |
| Working hours | 8:30am – 5:00pm (weekdays) |
| Weekly task cap | 10 hours |
| Dead task threshold | Overdue 30+ days, no description, no subtasks |
| Stall threshold | 14+ days since creation with no subtasks/description added |
| Priority weight | Job Search → Auburn admin → Encore consulting |
| Report path | `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/organizer-YYYY-MM-DD.md` |
| Parking Lot project | Jeff's existing "Parking Lot" project |
| Won't Do project | Jeff's existing "Won't Do" project |

---

## Scope

**Default scope — all three primary project trees:**
1. Professional Development (and all subfolders/subprojects) — highest weight
2. Family (and all subfolders)
3. Encore consulting (and all subfolders)

**Scoped run** — Jeff can limit to one project at session start:
> "Run the Organizer on just Return to Auburn"
> "Organizer — AI Agents only"

If scoped, skip project health check for out-of-scope projects and note the gap in the session report.

**Always excluded from writes:** Tasks assigned to [PARTNER] (UID `[PARTNER_TODOIST_UID]`). Review them for issues (missing dates, duplicates, conflicts) but surface findings as coordination prompts only — never write to them.

---

## SESSION START

Before pulling data, ask one question only:

> "Any fixed blocks this week I should know about for scheduling? (meetings, travel, ship ops — 2 sentences, or skip)"

Wait for the answer or "skip" before proceeding. This is the only manual input required.

---

## STEP 1 — Pull All Tasks

Pull all active tasks from the three primary project trees. Use `responsibleUserFiltering: all` and paginate if `hasMore: true`.

Pull in parallel if the MCP supports it. Note any projects where the pull returns zero tasks.

---

## STEP 2 — Project Health Check

For each project and subproject, produce a one-line health status:

| Status | Criteria |
|--------|----------|
| 🟢 Active | Has tasks with due dates in the next 14 days, or completions in the last 7 days |
| 🟡 Stalled | No completions in 14+ days AND no due dates in next 14 days |
| 🔴 Overdue-heavy | 3+ tasks overdue by 7+ days |
| ⚪ Empty | No open tasks |

Display as a compact table at the top of the session plan. This gives Jeff a 10-second read on where the problems are before diving into task-level detail.

---

## STEP 3 — Dead Task Detection

Flag any task that meets ALL of the following:
- Overdue by 30+ days
- No description
- No subtasks
- No labels suggesting it's a habit or recurring task

**Do not auto-delete.** Present flagged tasks in a "Proposed kills" section with three options for each:
- ❌ Kill it (delete)
- 🅿️ Park it (move to Parking Lot)
- 🚫 Won't Do (move to Won't Do project)

Jeff selects per task at confirm time, or says "kill all" / "park all" to batch-apply.

---

## STEP 4 — Duplicate Detection

Scan for tasks that appear to describe the same work. Use semantic similarity — don't just match on identical words. Flag pairs or clusters:

> "These look like the same task — which is the real one?"
> - "Research health insurance options" (Professional Development, no date)
> - "Look into health coverage for Auburn" (Return to Auburn, no date)

Present options: keep one and delete the other, or make one a subtask of the other. Jeff decides. Don't merge or delete until confirmed.

---

## STEP 5 — Vague Task Flagging

Flag tasks that lack enough information to execute — specifically:
- Task name is a noun phrase with no action verb ("Marketing stuff", "Q3 planning")
- Complex-sounding task with no description and no subtasks
- Task that clearly requires a decision or external input but has no context

For each vague task, note: "Needs Walk & Talk clarification" and include it in the Walk & Talk handoff list at the end of the session.

**Important:** Don't skip these tasks in prioritization — assign a best-guess priority and label, note it as provisional, and flag for clarification. A vague task that's clearly important doesn't get deprioritized just because it's unclear.

---

## STEP 6 — AI Leverage Labeling

Apply one of three labels to every non-trivial task (skip for simple single-action tasks like "Buy stamps"):

| Label | Meaning | Examples |
|-------|---------|---------|
| 🤖 Claude can lead | Claude produces 90%+ of the output; Jeff reviews and approves | Research, first drafts, proposals, app/widget builds, Claude Code prompts, option analyses, comparison matrices |
| 🤝 Claude can assist | Claude does 50–70%, Jeff provides domain judgment or decision | Strategy reviews, emails requiring relationship context, financial decisions |
| 👤 Jeff must lead | Relationship, execution, or judgment that can't be delegated | Calls, in-person decisions, anything requiring Jeff's presence or authority |

For every 🤖 task, also note the **proposed first action** Claude will take if Jeff says "go" during or after the session:
- Research task → "I'll produce a comparison matrix with top 3 options"
- Writing task → "I'll draft a first version for your review"
- App/widget task → "I'll write the Claude Code prompt and save it to this task"

**`burntokens` sub-label:** Within the 🤖 set, apply the `burntokens` label to any task where AI (Claude or Gemini) can complete 95%+ of the work with zero input or direction from Jeff — no decision needed, no context to extract, no relationship judgment required. These tasks can be executed autonomously in a batch without interrupting Jeff.

Criteria for `burntokens`:
- Claude has enough context in the task description (or connected tools) to proceed without asking anything
- Output requires no approval before being useful (or approval is a quick yes/no)
- No relationship context, financial authority, or Jeff's physical presence is needed

Examples: research and comparison matrix generation, first-draft writing with a clear brief, app/widget builds with a complete spec, data pulls and summaries from connected tools.

**Do not apply `burntokens` to:** anything requiring a decision Jeff hasn't made, tasks where output goes directly to a third party without review, or tasks that overwrite existing files without a diff.

Use existing Todoist labels where they match. If a new label is needed, flag it and ask Jeff before creating.

---

## STEP 7 — Priority Assignment

Apply Todoist priority levels (P1–P4) to every task, using this framework:

| Priority | Meaning | Typical tasks |
|----------|---------|--------------|
| P1 | Must do this week — urgent + important | Overdue items, hard deadlines in next 7 days, blockers for others |
| P2 | Important this week — not yet urgent | Advancing job search, move prep with upcoming deadlines, Encore consulting deliverables |
| P3 | This month — important, not urgent | Strategic projects, deck work, research |
| P4 | Recurring / habit / nice-to-have | Daily journaling, fitness, low-stakes admin |

**Priority weighting across domains:**
1. Job Search (Professional Development) — highest
2. Family / Move (Return to Auburn, Family projects)
3. Encore consulting — bridge engagement; light start, fuller cadence from mid-Oct

**Quadrant rule:** Explicitly label tasks that are important but not urgent (P2/P3 with no near date) as "high-value, no date" and force a due date proposal. These are the tasks that Todoist bankruptcy buries. Don't let them disappear.

Show Jeff a before/after priority summary: how many tasks changed at each level.

---

## STEP 8 — Scheduling

Assign a start date and time to all P1 and P2 tasks for the current week.

**Rules:**
- Working hours: 8:30am – 5:00pm weekdays
- Family tasks: evenings and weekends acceptable
- Weekly cap: 10 hours total (including fixed commitments from session start)
- If no time estimate is on a task, apply defaults:
  - Simple (single action): 30 min
  - Medium (multi-step, no research): 1 hr
  - Complex (research, writing, decision-heavy): 2 hr
- Assign earlier in the week for higher-priority tasks
- If the week is over-committed, surface the overflow explicitly: "You have [N] hours of tasks against a 10-hour cap. I'm deferring these to next week: [list]. Here's why."

**Never silently drop or defer a task.** Always tell Jeff what was moved and why.

**Calendar note:** No calendar integration yet. Scheduling is based on Jeff's stated working hours and fixed commitments provided at session start. Once calendar integration is available, this step hands off to the `weekly-planner` skill (see `handoff.md`).

**P3/P4 tasks:** Propose a due date (not a time) if none exists. Suggest month-level timing for P3. Leave P4 without a date unless Jeff specifies.

---

## STEP 9 — Project Move Proposals

Flag any task that clearly belongs in a different project:
- Inbox tasks that belong in a named project
- Move tasks sitting in Professional Development instead of Return to Auburn
- Job search tasks buried in a general folder

For each: explain why the move makes sense. Propose the destination. Do not move until confirmed.

---

## STEP 10 — Confirm + Write

Present the full session plan as a structured summary before writing anything:

```
ORGANIZER SESSION PLAN — [DATE]

PROJECT HEALTH
[table from Step 2]

PROPOSED CHANGES ([N] total)

Kills / Parks / Won't Do ([N])
- "[Task]" → [Kill / Park / Won't Do] — [reason]

Duplicate resolution ([N])
- "[Task A]" + "[Task B]" → [keep A, make B subtask / keep A, delete B] — your call

Priority changes ([N])
- "[Task]" → P[X] (was P[Y]) — [reason]

Scheduling ([N])
- "[Task]" → [Day], [Time], [Duration]

Project moves ([N])
- "[Task]" → [Destination project] — [reason]

AI leverage labels ([N])
- 🤖 "[Task]" — [proposed first action if executed]
- 🤖🔥 "[Task]" — burntokens: [what Claude will do autonomously, no input needed]
- 🤝 "[Task]" — [what Jeff needs to provide]

Needs Walk & Talk clarification ([N])
- "[Task]" — [what's unclear]

[PARTNER] coordination prompts ([N])
- "[Task]" — [what to check with [PARTNER]]

---
Ready to write. Say "go" to apply all changes, or call out anything to adjust first.
```

Wait for "go" or explicit approval before writing.

**Write rules:**
- Labels, priority changes, scheduling → write all in one shot after "go"
- Kills, description overwrites, project moves → confirm task-by-task within the session plan (Jeff can pre-approve by saying "kill all flagged" etc.)
- Never auto-delete
- Never overwrite an existing description without showing the diff

---

## STEP 11 — Session Report

Save a markdown report after writing:

**Path:** `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/organizer-YYYY-MM-DD.md`

**Report structure:**
```
# Todoist Organizer Session — [DATE]

## Summary
- Tasks reviewed: [N]
- Priority changes: [N]
- Tasks scheduled: [N]
- Killed / Parked / Won't Do: [N]
- Duplicates resolved: [N]
- 🤖 Claude-ready tasks flagged: [N]
- Needs Walk & Talk: [N]
- [PARTNER] coordination prompts: [N]

## Project Health
[table]

## Changes Made
[full list of every write, with before/after]

## Walk & Talk Handoff
[list of tasks flagged for clarification — ready to paste into Walk & Talk Interviewer]

## 🤖 Claude-Ready Queue
[list of tasks labeled 🤖, with proposed first action — ready to execute on request]

## 🔥 burntokens Queue
[list of tasks labeled burntokens — Claude can run these with zero input from Jeff. Surface during next Walk & Talk pull for batch execution.]
```

Confirm the file path to Jeff after saving.

---

## Integration with Other CoS Skills

**Runs after:** `walk-and-talk-interviewer` (Walk & Talk extracts context → Organizer uses it to prioritize and clean)

**Feeds into:** `execution-shadow` (planned) — the 🤖 Claude-Ready Queue from the Organizer's report is the input list for the Execution Shadow's work session

**Feeds into:** `weekly-review` — Organizer replaces the Todoist Load Plan step in weekly-review. Weekly-review calls Organizer or imports the session report rather than doing its own task scheduling.

**Sequential flow (full CoS session):**
1. Walk & Talk Interviewer → clarifies and enriches tasks
2. Todoist Organizer → prioritizes, cleans, labels, schedules
3. Execution Shadow → takes the 🤖 queue and starts doing the work

---

## Known Limitations (May 2026)

- **No calendar integration:** Scheduling is based on stated working hours + session-start commitments only. See `handoff.md` for the `weekly-planner` skill planned once calendar connects.
- **No last-activity timestamps from Todoist MCP:** Stall detection uses creation date as proxy. Jeff can dismiss incorrectly flagged tasks at confirm time.
- **Existing Todoist labels:** Organizer uses Jeff's existing labels only. Any new label requires Jeff's approval. Current label set should be verified at first run.
- **Asana:** Ignored entirely. If Jeff pastes an Asana CSV manually, note it in the session report but don't integrate into the Organizer's logic.
- **[PARTNER]'s tasks:** Can review but never write. All [PARTNER] findings surface as coordination prompts only.

---

## Session Trigger Phrases

Run this skill when Jeff says any of:
- "Organize my Todoist"
- "Run the organizer"
- "Let's clean up Todoist"
- "Do a Todoist audit"
- "Prioritize my tasks"
- "Run the full CoS review" (triggers Walk & Talk → Organizer in sequence)
- Called by `weekly-review` skill for the load plan step

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-19 | v1.0 — created. Built from Jeff's walk transcript + Gemini CoS design session. Phase 2 of 3-phase CoS architecture. |
| 2026-05-31 | v1.1 — added `burntokens` sub-label to AI leverage labeling (Step 6). Tasks meeting 95%+ autonomous threshold get both 🤖 and burntokens labels. Added burntokens queue to session report. Updated session plan format to surface burntokens tasks distinctly. |
