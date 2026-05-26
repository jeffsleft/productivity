---
name: cos-sync
description: >
  Run the full 3-phase Chief of Staff session in sequence: Walk & Talk Interviewer (context extraction) → Todoist Organizer (prioritization and cleanup) → Execution Shadow (doing the work). Use this skill when Jeff says "run the full CoS review", "CoS Sync", "run CoS Sync", "full weekly review", "run all three phases", "start my CoS session", or when triggered by a scheduled task. Individual sub-skills remain callable independently — this orchestrator sequences them and handles handoffs automatically.
---

# CoS Sync — Full Session Orchestrator
### Version 1.0 — May 2026
### Part of: CoS Plugin
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP
### Orchestrates: walk-and-talk-interviewer → todoist-organizer → execution-shadow

---

## NAME NOTE

This skill is saved as `cos-sync` per the recommendation in `handoff.md`. If Jeff picks a different name from the five options listed there, rename the folder and update the `name:` field in this file's frontmatter. The five options were:

| Name | Slug |
|------|------|
| Daily Ops Review | `daily-ops-review` |
| **The Debrief** | `debrief` |
| **CoS Sync** | `cos-sync` ← current |
| The Triage | `triage` |
| Ground Truth | `ground-truth` |

---

## What This Skill Does

Sequences the full 3-phase CoS daily-operations session. The sub-skills do all the heavy lifting — this orchestrator manages pacing, state passing, and the consolidated close.

**Phase 1 — Walk & Talk Interviewer:** Pulls Todoist + Apple Notes. Generates interview questions. Sends Jeff on a walk. Processes transcript. Writes clarified tasks back to Todoist.

**Phase 2 — Todoist Organizer:** Autonomous audit pass. Cleans dead tasks, resolves duplicates, applies P1–P4 priorities, assigns AI leverage labels (🤖/🤝/👤), schedules the week, proposes project moves. One confirm step before writing.

**Phase 3 — Execution Shadow:** Takes the 🤖 Claude-Ready Queue from the Organizer. Generates proposals, runs research spikes, writes Claude Code prompts, executes work within the session.

Each phase is pause-able. Jeff can stop after any phase and resume later. The orchestrator saves state to the session report so nothing is lost.

---

## Key Constants

| Constant | Value |
|----------|-------|
| Jeff's Todoist UID | `14075501` |
| Jaclyn's Todoist UID | `16889336` — never touch |
| Session report path | `/Users/jeffbeaumont/Projects/Professional Development/cos-plugin/reports/cos-sync-YYYY-MM-DD.md` |
| Sub-skill report paths | `walk-talk-YYYY-MM-DD.md`, `organizer-YYYY-MM-DD.md`, `execution-shadow-YYYY-MM-DD.md` |
| Priority weight | Job Search → Family/Move → Mercy Ships |

---

## SESSION OPEN

Ask three questions before pulling any data. Keep it fast — one block, not three separate exchanges:

```
CoS Sync — [DATE]

Three quick questions before we start:

1. Scope: Full (all projects) or scoped to one project?
   (default: full — Job Search, Family/Move, Mercy Ships)

2. Fixed blocks this week I should know about for scheduling?
   (meetings, travel, ship ops — skip if none)

3. Walk & Talk: Do you have a transcript ready, or do you want to
   go for a walk first?
   (a) I have a transcript / I'll paste it
   (b) Generate the interview script — I'll walk first, then come back
   (c) Skip Walk & Talk — run Organizer and Shadow only
```

Wait for Jeff's answers before pulling any data. Confirm the plan in one line:

> "Got it — [scope], [fixed blocks or 'no fixed blocks'], [Walk & Talk mode]. Starting Phase 1."

---

## PHASE 1 — Walk & Talk Interviewer

Invoke the `walk-and-talk-interviewer` skill with the session context already established (scope, date, fixed blocks).

**If transcript is ready or Jeff will paste it:**
- Pull Todoist + Apple Notes immediately
- Generate interview questions
- Process transcript when it arrives
- Write clarified tasks back to Todoist (with confirm)
- Produce the Organizer handoff list
- Transition to Phase 2 automatically

**If generating script for a walk:**
- Pull Todoist + Apple Notes
- Generate the interview script
- Tell Jeff: "Here's your script. Go walk. When you're back, paste the transcript or say 'I saved it to Apple Notes' and I'll pull it. I'll be here."
- **Pause the session.** Do not proceed to Phase 2 until Jeff returns.
- On return: process transcript → write back → transition to Phase 2

**If skipping Walk & Talk (option c):**
- Note the skip in the session report
- Pass any Walk & Talk handoff from a prior session if available in context
- Proceed directly to Phase 2

**State to carry forward into Phase 2:**
- List of tasks clarified and written this session
- Any tasks still needing clarification (for Walk & Talk handoff at session close)
- Session date and scope (already established)
- Fixed blocks (already established)

---

## PHASE 2 — Todoist Organizer

Invoke the `todoist-organizer` skill. Pass in:
- Session date
- Fixed blocks (from session open)
- Scope
- Walk & Talk handoff list (tasks just clarified — Organizer should note these as "recently clarified, full context available")

The Organizer runs its full autonomous pass. At the end, it presents the full session plan and waits for Jeff's "go" before writing.

**After Organizer writes:**
- Capture the 🤖 Claude-Ready Queue from the Organizer's session report
- Note the count: "[N] tasks in your 🤖 queue, ready for Phase 3."

**If the 🤖 queue is empty:**
- Report it: "No 🤖 tasks flagged this session. Phase 3 is a no-op — I'll close out the session now."
- Skip Phase 3 and go directly to session close.

**State to carry forward into Phase 3:**
- Full 🤖 Claude-Ready Queue (task names, project, priority, proposed first action)
- Organizer session report path (for reference)
- Any Walk & Talk handoff tasks that still need clarification

---

## PHASE 3 — Execution Shadow

Invoke the `execution-shadow` skill with the 🤖 Claude-Ready Queue already loaded — do not require a Todoist re-pull.

Present the queue (as the Shadow's Step 1 does), but add a session-time note:

```
PHASE 3 — EXECUTION SHADOW
[N] tasks in your 🤖 Claude-Ready Queue

We've already run Phase 1 and 2 — your tasks are clean and prioritized.
Now let's actually do some of them.

[queue display per execution-shadow Step 1 format]

How do you want to proceed?
  "go all" — work through the queue top to bottom
  "go #[N]" — start at a specific task
  "skip Phase 3" — close out the session now
```

Run the Shadow per its own SKILL.md. No modifications to Shadow behavior needed here.

**If Jeff skips Phase 3:**
- Note it in the session report: "Phase 3 deferred — 🤖 queue available for standalone Execution Shadow session."
- Proceed to session close.

---

## SESSION CLOSE

After all phases complete (or after any phase Jeff stops at), produce a consolidated session summary:

```
CoS Sync — Session Close — [DATE]

PHASES COMPLETED
✅ Phase 1 — Walk & Talk: [N] tasks clarified, [N] written to Todoist, [N] killed
✅ Phase 2 — Organizer: [N] changes written, [N] 🤖 tasks flagged, [N] scheduled
✅ Phase 3 — Execution Shadow: [N] tasks executed, [N] Claude Code prompts written
(or ⏭ Phase [N] — Skipped)

TOP 3 ACTIONS THIS WEEK
[Pulled from P1 tasks — the 3 most important things Jeff needs to do]
1. [Task name] (Project) — due [date]
2. [Task name] (Project) — due [date]
3. [Task name] (Project) — due [date]

🤖 QUEUE STATUS
[N] tasks executed this session
[N] tasks remaining in queue (run "execution shadow" anytime to continue)

STILL NEEDS WALK & TALK
[list of tasks flagged for clarification, if any]

JACLYN COORDINATION PROMPTS
[list of Jaclyn tasks with coordination flags, if any]
```

Save the consolidated report:

**Path:** `/Users/jeffbeaumont/Projects/Professional Development/cos-plugin/reports/cos-sync-YYYY-MM-DD.md`

The report should embed or link the sub-skill reports rather than duplicating them in full:
- Walk & Talk report: `walk-talk-YYYY-MM-DD.md`
- Organizer report: `organizer-YYYY-MM-DD.md`
- Execution Shadow report: `execution-shadow-YYYY-MM-DD.md`

Confirm the consolidated report path to Jeff after saving.

### Slack notification — #chief-of-staff

After saving the report, post a summary to the `#chief-of-staff` Slack channel using the Slack MCP. Keep it to 3 lines max — this is a status ping, not a report.

**Format:**
```
CoS Sync complete — [DATE]
✅ [N] tasks clarified | [N] Todoist changes | [N] 🤖 tasks executed
Top action: [single most important P1 task this week]
```

**If Slack MCP is unavailable:** Skip silently. Note in the session report: "Slack notification not sent — MCP unavailable." Do not block session close on Slack.

**Channel:** `#chief-of-staff`

---

## STATE PASSING BETWEEN PHASES

The sub-skills don't natively pass state — the orchestrator holds it in context:

| From | To | State passed |
|------|----|-------------|
| Session open | Phase 1 | Scope, date, fixed blocks, Walk & Talk mode |
| Phase 1 → Phase 2 | Walk & Talk handoff list, session date, scope, fixed blocks |
| Phase 2 → Phase 3 | 🤖 Claude-Ready Queue (full list with task IDs, project, priority, proposed action) |
| Phase 3 → Close | Execution summary (tasks done, prompts written, deferred items) |

If the session is interrupted between phases, the orchestrator saves intermediate state to the session report file so the next session can resume without re-running completed phases.

**Resuming an interrupted session:** If Jeff says "resume my CoS session" or "pick up where we left off," read the most recent `cos-sync-YYYY-MM-DD.md` report, identify which phase was last completed, and offer to continue from the next phase.

---

## SCHEDULING

**Scheduled:** Every Friday at 10:00am (task ID: `cos-sync-friday` in Claude Scheduled sidebar).

Trigger phrase: "Run CoS Sync"

To change cadence: open Scheduled sidebar → cos-sync-friday → edit.

**Note:** The existing `friday-cos-review` skill is a separate workflow (Happy Friday email, Todoist Organizer, thread triage). CoS Sync is the daily-operations CoS session — different scope and outputs. They can coexist. Jeff may want to run CoS Sync earlier in the day on Fridays before `friday-cos-review`.

---

## GRACEFUL DEGRADATION

| Missing sub-skill | Behavior |
|-------------------|---------|
| `walk-and-talk-interviewer` not built | Skip Phase 1, note the gap, proceed with Organizer using current Todoist state |
| `todoist-organizer` not built | Skip Phase 2, note the gap — pull 🤖-labeled tasks directly for Phase 3 |
| `execution-shadow` not built | Skip Phase 3, display the 🤖 queue as a plain list and say "Execution Shadow not yet built — run these manually or say 'go' on any item to start now" |

As of May 2026: all three sub-skills are built. Graceful degradation is future-proofing.

---

## Known Limitations (May 2026)

- **No calendar integration:** Scheduling in Phase 2 uses working hours heuristic. See `handoff.md` for `weekly-planner` skill planned once calendar connects.
- **Session length:** Full 3-phase sessions can run 45–90 minutes depending on task volume. Jeff can stop at any phase boundary without losing work.
- **Notion pull:** Currently not included. Notion MCP is intermittent. If Notion becomes a primary workspace, add a Notion pull to the session open before Phase 1.
- **friday-cos-review coexistence:** CoS Sync and the existing `friday-cos-review` skill overlap on Fridays. Until Jeff decides which replaces which, run CoS Sync for daily ops and `friday-cos-review` for the Happy Friday email + snapshot.

---

## Session Trigger Phrases

Run this skill when Jeff says any of:
- "Run the full CoS review"
- "CoS Sync" / "Run CoS Sync"
- "Full weekly review"
- "Run all three phases"
- "Start my CoS session"
- "Resume my CoS session"
- Called by a scheduled task

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-19 | v1.0 — created. Orchestrates all 3 phases of the CoS daily-operations system. Built from spec in handoff.md. Name pending Jeff's selection — defaulting to cos-sync per handoff.md recommendation. |
