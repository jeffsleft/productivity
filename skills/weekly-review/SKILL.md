---
name: weekly-review
description: "Run a structured Sunday review across Todoist, Apple Notes, the Master Projects sheet, and calendar. Combines a strategic altitude check (am I working on the right things?) with the full operational Todoist audit that assigns priorities/dates and schedules the week's tasks. Priority areas: finding a job, Auburn admin close-out, and the Encore consulting bridge. Produces a weekly recap, program-alignment gate, full task audit + load plan, app & AI work tracker, decision accountability, 2-week horizon scan, and a brief Auburn pulse. Trigger when Jeff says 'run my weekly review', 'weekly review', 'strategic review', 'Sunday review', or 'altitude check'."
version: 2.4
---

# Weekly Review Skill
### Version 2.4 — July 2026
### Part of: Weekly Review Plugin (standalone, strategic track)
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP + Google Drive (Master Projects sheet) + Google Calendar MCP

---

## What This Skill Does

The **Sunday** review. Two jobs in one sitting:

1. **Strategic altitude** — *am I actually moving on the things that matter?* Checked against the **Master Projects sheet** (the program/goals layer), not just the task list.
2. **Operational planning** — the **full Todoist audit** that assigns priorities/dates/labels to open tasks and **schedules the week ahead** (~100 tasks). This is the point of Sunday.

Priorities (current):
1. Finding a job (primary)
2. Auburn admin close-out (resettlement admin — the physical move is complete)
3. Encore consulting bridge

Run **Sunday ~4pm**. Produces a saved markdown report plus the outputs below.

**Replaces:** `friday-cos-review` (retired May 2026). Mercy Ships finance role **concluded July 3, 2026** — all Mercy-Ships recurring items (GLM Finance Update, Happy Friday email) are retired from this skill.

---

## Track Separation — Read This First

| Track | Plugin | Skill | Cadence | Question |
|---|---|---|---|---|
| **Operational (mid-week)** | CoS plugin | `cos-sync` | Friday | "Is my task list clean and worked mid-week?" |
| **Strategic + weekly planning** | Weekly Review plugin | `weekly-review` (this) | Sunday 4pm | "Am I on the right things — and is the week ahead assigned?" |

Sunday and Friday are **both** operational, on purpose: Sunday **plans and assigns the week ahead** (full audit + schedule); Friday **cleans up and executes** mid-week. Different jobs, same task list. Do not treat Friday as a substitute for the Sunday assignment pass, or vice versa.

**Two-button rhythm:** Jeff runs ONE button Sunday (this skill — strategic + weekly assignment) and ONE button Friday (`cos-sync` — mid-week cleanup/execution). Every other skill is a *section called by* one of these.

**Global rule — enumerate options:** anywhere this review asks Jeff to defer, cut, reprioritize, or decide, present a **numbered list of concrete options**, not an open question. He decides from a list, not an implied set.

**Jeff-now brief (pending build):** once the one-page "Jeff-now" brief exists, read it at the top of Step 1 and refresh it in Step 10. Until built, skip. (Orphaned-artifact rule: it only survives if it is read here every run.)

---

## STEP 1 — Confirm Date and Collect Manual Inputs

Confirm today's full date (e.g., "Sunday, July 19, 2026"). Sets the 8-day lookback and 14-day forward window.

Prompt:
> *"Any fixed commitments this week I should know about for scheduling? (meetings, travel, family — 2–3 lines, or skip)"*

Wait for the response before pulling data.

---

## STEP 2 — Data Pull (run in parallel)

**Todoist:**
- Completed tasks — last 8 days
- **ALL open/active tasks across all projects** — for the full operational audit (Step 5)
- Upcoming tasks — next 14 days
- Return to Auburn project — full task list
- Tasks with label `#blog` or `%makethebotwork`
- All tasks in the AI Agents project

**Master Projects sheet (Google Drive):** pull the program/goals layer (see auto-memory → *Master Projects Sheet* for the sheet ID + schema). Needed for the Program Alignment gate (Step 4).

**Apple Notes:** notes modified in the last 8 days from Voice Memo Reflections, Professional Development (+ subfolders), Family Shared (lower weight — only if substantive). Filter by timestamp after pulling. If MCP is down, note the gap and continue.

**Calendar:** events for the next 14 days — feeds Horizon Scan (Step 8) and scheduling conflict avoidance.

No Notion pull.

---

## STEP 3 — Output 1: Weekly Recap

**Hard limit: 250 characters.** What happened, what mattered, one insight. One paragraph, no bullets. Drawn from completed tasks + Apple Notes.

---

## STEP 4 — Output 2: Program Alignment Gate (the altitude check)

Compare where time actually went (completed tasks, Apple Notes) and the upcoming load against the **Master Projects sheet**. This is the advisory move — the shiny-object gate.

- For each active program/project: on track / stalled / drifting?
- **Flag anything Jeff is spending time on that is NOT tied to a tracked program.** For each, classify (numbered): (1) important — promote to a tracked program, (2) important but undocumented — log it, (3) not important — backlog or drop.
- Surface at most the 3 sharpest misalignments. If aligned, say so in one line.

---

## STEP 5 — Output 3: Full Todoist Audit + Load Plan (the weekly assignment)

The operational core of Sunday. Two parts.

**5a — Audit all open tasks** (todoist-organizer logic):
- Kill dead tasks; flag vague ones (numbered, for Jeff's call)
- Apply priority (P1–P4) and AI-leverage labels (🤖 / 🤝 / 👤)
- Propose due dates for undated tasks; detect duplicates; flag project moves
- Propose promotions along `idea → makethebotwork → burntokens` where specs are now complete (Jeff confirms)

**5b — Load plan / schedule the week:**
- **Daily cap: 8.5 h/day (8:30am–5:00pm).**
- Exclude "BLOCK - DNB" / "Focus Time".
- Use Step 1 commitments + calendar for conflict avoidance.
- **Priority weight: Job Search → Auburn admin → Encore consulting.**
- Day-by-day list with time estimates, running daily total vs. the cap, one-line verdict per day (*under / at / over*). Flag overflow and give a **numbered** defer list.
- Time-estimate defaults: simple 30 min, medium 1 h, complex 2 h.

The load plan is persisted (Step 10) as the ceiling `cos-sync` reads Friday.

---

## STEP 6 — Output 4: App & AI Work Tracker

Scope: AI Agents project + `#blog` + `%makethebotwork`. Bucket each: **Claude-autonomous** / **Needs Jeff's decision** / **Requires Jeff's time**. Format: Item | Bucket | Next action. Flag anything waiting on a Jeff decision 7+ days.

---

## STEP 7 — Output 5: Decision Accountability (standing section)

Call `decision-accountability` (CoS plugin), run Steps 1–3 silently, and insert:
- Logged decisions due for follow-up (>14 days, no follow-up entry)
- Open Todoist tasks that contradict a logged decision
- Silently abandoned decisions (>30 days, no action)

**Plus — unlogged decisions (new in v2.4):** scan the last 8 days of Apple Notes, completed tasks, and this session for decision-shaped language (chose / decided / going with / killed / committed) that is NOT in the Decision Log. For each, ask (numbered) whether to log it. Catching un-logged decisions is the higher-value half — the log has gone stale before by only tracking what was already in it.

If `decision-accountability` is unavailable, read the `CoS — Decision Log` Apple Note directly and apply the same thresholds. If nothing needs attention, one line.

---

## STEP 8 — Output 6: Horizon Scan (2-week lookahead)

Scan the next 14 days across calendar, Todoist due dates, Return to Auburn, and the current priorities.

**Known deadline-bearing obligations to check every run:**
- **School enrollment** — Auburn, deadline August 2026. Escalate as it approaches if not started.
- **Auburn admin close-out** — insurance (health, homeowners, vehicle), utilities activation, tenant walkthrough. Flag any with a hard date and no active task.
- **Encore consulting** — flag deliverable dates / Ian dependencies as they approach.
- **Job search** — interview loops, follow-ups, references — flag anything time-sensitive with no next action.

Format (max 6 lines): 🔴 ≤7 days, no task/prep · 🟠 8–14 days, prep now · 🟡 known horizon item with no active task as its window narrows. If clear: *"Horizon clear — no unflagged deadlines in the next 14 days."*

Reads the horizon; does not act.

---

## STEP 9 — Output 7: Auburn Pulse (brief)

The physical move is complete; this tracks remaining admin. 3–5 lines from the Return to Auburn project:
- 🔴 anything due within 30 days
- 🟠 any [PARTNER]-owned item with no movement in 14+ days (coordination prompt, not criticism)
- 🟡 count of critical no-date items (insurance, utilities, tenant walkthrough, school enrollment)

If all clear, one line. (The standalone `ca-move-pulse` skill was retired 2026-08-19 — this section is now the only Auburn read.)

---

## STEP 10 — Persist Memory + Save Report + Load-Plan Artifact

**Memory pass:** ask *"Anything from this review worth persisting to memory?"* — decisions, preferences, gotchas that should outlive the session. If yes, write to auto-memory.

**Save report:** `[workspace]/weekly-review-plugin/reports/weekly-report-YYYY-MM-DD-HH-MM-SS.md`, where `[workspace]` = `[YOUR_PROJECTS_PATH]/Professional Development/`.

**Save load-plan artifact (handoff to cos-sync):** `[workspace]/weekly-review-plugin/load-plan-current.md`. Overwrite each run; date-stamp inside. This is the single ceiling Friday `cos-sync` reads.

Confirm both paths to the user.

---

## Known Limitations
- **Job search pipeline:** not a dedicated skill yet (planned). Surfaced here via priorities + horizon scan.
- **Relationship-debt tracking:** deferred → revisit ~October 2026. No who's-owed-a-reply / cooling-relationship layer yet.
- **Apple Notes MCP:** date filtering is done manually from modification timestamps.
- **Master Projects sheet:** Drive MCP is read-only for Sheets — propose updates for Jeff to paste; no direct writes.

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-18 | v1.0 — created. Replaced friday-cos-review. Trimmed to 5 outputs. |
| 2026-06-03 | v2.0 — Migrated into Compass plugin. Corrected daily cap to 8.5h/day. Added decision-accountability and reschedule-detector standing sections. Added memory-persist pass. Renamed Agents & NanoClaw → AI Agents. Added track-separation note. |
| 2026-06-05 | v2.1 — Extracted to its own standalone Weekly Review plugin. Added Horizon Scan (2-week lookahead). Added load-plan artifact handoff to cos-sync. Added calendar pull. Documented relationship-debt as a deferred gap. |
| 2026-06-29 | v2.2 — Removed Asana Cross-Functional Tracker (Mercy-Ships-only). Now 7 outputs. |
| 2026-07-15 | v2.3 — Removed Reschedule Detector (killed by Jeff — do not reintroduce). Overcommit signal lives in Sunday planning. |
| 2026-07-16 | v2.4 — **Moved to Sunday 4pm (from Monday).** Retired all Mercy Ships content (role concluded July 3): removed GLM Finance Update, Happy Friday email, CPA/PT licensing, "recurring Mercy Ships ops" from the cap, and the Mercy Ships priority. New priorities: Job → Auburn admin → Encore. **Added Program Alignment gate** (Master Projects sheet / shiny-object gate). **Merged in the full operational Todoist audit + weekly assignment** (todoist-organizer logic) — the point of Sunday. Added **unlogged-decision catch** to decision accountability. Added global **enumerate-options** rule and a **Jeff-now-brief** slot (pending build). Renamed Move Pulse → Auburn Pulse. |
