---
name: cos-sync-friday
description: Weekly Friday CoS Sync — full 3-phase Chief of Staff session
---

Run the full CoS Sync session — all 3 phases in sequence.

## Objective
Execute the weekly Chief of Staff daily-operations review using the `cos-sync` skill from the CoS plugin. This is Jeff's weekly session to clarify tasks, organize Todoist, and execute on AI-ready work.

## What to do
Invoke the `cos-sync` skill. It will sequence:
1. Walk & Talk Interviewer — pulls Todoist + Apple Notes, generates interview questions or processes a transcript, writes clarified tasks back to Todoist
2. Todoist Organizer — autonomous audit: dead tasks, duplicates, P1–P4 priorities, AI leverage labels (🤖/🤝/👤), scheduling, project moves. One confirm before writing.
3. Execution Shadow — takes the 🤖 Claude-Ready Queue from the Organizer, generates proposals, runs research spikes, writes Claude Code prompts, executes work within session.

## Session open questions (ask Jeff at start)
1. Scope: full (all projects) or scoped to one project?
2. Fixed blocks this week for scheduling? (meetings, travel — skip if none)
3. Walk & Talk: transcript ready, or generate script for a walk first?

## Key constraints
- Jeff's Todoist UID: 14075501. Jaclyn's UID: 16889336 — never modify Jaclyn's tasks.
- All Todoist writes require Jeff's confirm before executing.
- Priority weight: Job Search → Family/Move → Mercy Ships.
- Existing Todoist labels only — ask before creating new ones.
- Keep task descriptions short (1–2 sentences). Claude Code prompts are the exception — write in full.

## Tools needed
- Todoist MCP (find-tasks, update-tasks, reschedule-tasks, add-tasks, complete-tasks)
- Apple Notes MCP (list_notes, get_note_content) — check `Professional Development → Voice Memo Reflections → Walk & Talk` for recent transcripts
- WebSearch / WebFetch — for Execution Shadow research spikes

## Output
Save consolidated session report to:
`/Users/jeffbeaumont/Projects/Professional Development/cos-plugin/reports/cos-sync-YYYY-MM-DD.md`
(replace YYYY-MM-DD with today's date)

Sub-skill reports also saved to same folder:
- `walk-talk-YYYY-MM-DD.md`
- `organizer-YYYY-MM-DD.md`
- `execution-shadow-YYYY-MM-DD.md`
