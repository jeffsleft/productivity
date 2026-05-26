---
name: weekly-review
description: "Run a structured weekly review across Todoist, Apple Notes, and manual inputs. Covers the three priority areas: finding a job, returning home with family, and finishing strong at Mercy Ships. Produces five outputs: weekly recap, Todoist load plan, app & AI work tracker, Mercy Ships cross-functional tracker, and a brief Move Pulse. Part of the CoS plugin. Trigger when user says 'run my weekly review', 'CoS review', 'weekly review', or 'run the CoS skill'."
---

# Weekly Review Skill
### Version 1.0 — May 2026
### Part of: CoS Plugin
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP

---

## What This Skill Does

A structured weekly review session focused on three priorities:
1. Finding a job
2. Returning home / family resettlement
3. Finishing strong at Mercy Ships

Produces five outputs plus a saved markdown report. Can be run any day of the week.

**Replaces:** `friday-cos-review` skill (retired May 2026)

---

## Skill Map — CoS Plugin

This skill is one of several planned under the CoS plugin:

| Skill | Status |
|-------|--------|
| `weekly-review` | Active (this skill) |
| `job-search-pipeline` | Planned |
| `notion-insights` | Planned |
| `ca-move-pulse` | Planned |
| `todoist-deep-dive` | Planned |

---

## STEP 1 — Confirm Date and Collect Manual Inputs

Confirm today's full date (e.g., "Monday, May 18, 2026"). Needed to set the 8-day lookback and 14-day forward window.

Then prompt:

> *"Two quick inputs before I pull data:*
> *1. Any fixed commitments this week I should know about for scheduling? (meetings, travel, ship ops — 2–3 lines, or skip)*
> *2. Paste your Asana CSV if you have cross-functional Mercy Ships projects to track (or skip)"*

Wait for both responses before pulling any data.

---

## STEP 2 — Data Pull (run in parallel)

**Todoist:**
- Completed tasks — last 8 days
- Upcoming tasks — next 14 days
- Return to Auburn project — full task list (for Move Pulse summary in Output 5)
- Tasks/items with label `#blog` or `%makethebotwork`
- All tasks in the Agents & NanoClaw project

**Apple Notes:**
- Pull all notes modified in the last 8 days from these folders:
  - Voice Memo Reflections
  - Professional Development (and subfolders)
  - Family Shared (lower weight — include only if substantive)
- Filter by modification timestamp after pulling — discard notes older than 8 days
- If Apple Notes MCP is not connected, note the gap and continue

No Notion pull in this skill. No project-synopsis prerequisite.

---

## STEP 3 — Output 1: Weekly Recap

**Hard limit: 250 characters.** What happened, what mattered, one key insight. One paragraph, no bullets. Draw from completed Todoist tasks and Apple Notes distillation.

---

## STEP 4 — Output 2: Todoist Load Plan

Schedule important upcoming tasks across the current week. Rules:

- **Cap: 10 hours total** — includes meetings and recurring Mercy Ships operational work
- **Exclude** any item labeled "BLOCK - DNB" or "Focus Time"
- **Use manual commitments** from Step 1 for conflict avoidance
- **Priority weight:** Job Search → Family/Move → Mercy Ships
- Flag anything that won't fit within the 10-hour cap and propose what to defer

**Format:**
- Day-by-day task list with estimated time per task
- Running weekly total
- One-line load verdict at the end: *under / at / over*

If time estimates are missing from Todoist tasks, apply these defaults:
- Simple task (single action): 30 min
- Medium task (multi-step, no research): 1 hr
- Complex task (research, writing, or decision-heavy): 2 hr

---

## STEP 5 — Output 3: App & AI Work Tracker

**Scope:** Agents & NanoClaw Todoist project + tasks with `#blog` label + tasks with `%makethebotwork` label.

Classify each item into one bucket:

| Bucket | Definition |
|--------|------------|
| **Claude handles autonomously** | Coding, research, drafting — no decision needed from Jeff |
| **Needs Jeff's decision** | Priority call, architectural choice, approval gate |
| **Requires Jeff's time** | Non-trivial input, review, or co-authoring that can't be delegated |

**Format:** Table — Item | Bucket | Suggested next action

Flag any item waiting on a Jeff decision for 7+ days.

---

## STEP 6 — Output 4: Mercy Ships Cross-Functional Tracker

**Only runs if Asana CSV was provided in Step 1.** If skipped, note it in one line and move on.

Parse the CSV. Surface:
- Items due in the next 14 days
- Items with no assigned owner
- Items marked at-risk or blocked

**Format:** Table — Item | Due Date | Status/Flag

---

## STEP 7 — Output 5: Move Pulse (brief)

Not the full `ca-move-pulse` skill. A 3–5 line flag only, pulled from the Return to Auburn Todoist project:

- 🔴 Anything due within 30 days
- 🟠 Any Jaclyn-owned items with no movement in 14+ days
- 🟡 Count of critical no-date items (insurance, licensing, shipping, school enrollment, utilities, tenant walkthrough)

If all clear, say so in one line. Full analysis lives in the separate `ca-move-pulse` skill.

---

## STEP 8 — Save Report

Save the full session output as a markdown file:

**Path:** `[workspace]/cos-plugin/reports/weekly-report-YYYY-MM-DD-HH-MM-SS.md`

Where `[workspace]` = `/Users/jeffbeaumont/Projects/Professional Development/`

Confirm the file path to the user after saving.

---

## Known Limitations

- **Calendar:** No calendar integration. Use manual commitments input at session start for scheduling context.
- **Job search pipeline:** Not included in this skill — planned as separate `job-search-pipeline` skill.
- **Notion insights:** Not included — planned as separate `notion-insights` skill.
- **Asana:** Manual CSV paste only. No live Asana MCP connection.
- **Apple Notes MCP:** Date filtering is done manually from modification timestamps after pulling — the list_notes tool does not support date filtering natively.
- **Email drafting:** Not part of this skill. Happy Friday email parked — no skill planned currently.

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-18 | v1.0 — created. Replaces friday-cos-review. Trimmed to 5 outputs. Dropped email, Notion pull, project-synopsis prereq, Claude Chat review, calendar integration. Added App & AI Work Tracker. Restructured as first skill under CoS plugin architecture. |
