---
name: "weekly-review"
description: "Run a structured Sunday review across Todoist, Apple Notes, the Master Projects sheet, and calendar. Combines a strategic altitude check (am I working on the right things?) with the full operational Todoist audit that assigns priorities/dates and schedules the week's tasks. Priority areas: the Encore consulting engagement, the job search, and Auburn admin close-out. Produces a weekly recap, program-alignment gate, full task audit + load plan, app & AI work tracker, decision accountability, 2-week horizon scan, a brief Auburn pulse, and a config integrity check. Trigger when Jeff says 'run my weekly review', 'weekly review', 'strategic review', 'Sunday review', or 'altitude check'."
---

# Weekly Review Skill
### Version 2.8 — October 2026
### Part of: Weekly Review Plugin (standalone, strategic track)
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP + Google Drive (Master Projects sheet) + Google Calendar MCP

---

## What This Skill Does

The **Sunday** review. Two jobs in one sitting:

1. **Strategic altitude** — *am I actually moving on the things that matter?* Checked against the **Master Projects sheet** (the program/goals layer), not just the task list.
2. **Operational planning** — the **Todoist audit** that assigns priorities/dates/labels and **schedules the week ahead**. This is the point of Sunday.

**Priorities (ruled 2026-09-06 — supersedes the prior order):**
1. **Encore consulting engagement** — executed contract, 13-week term, zero slack, ends 2026-11-28
2. **Job search** — both processes are late-stage and need *touchpoints, not depth*
3. **Home Setup & house build** — fence, retaining wall, shed
4. **Auburn admin close-out** — residual, has not moved in ten months

Revert to job-search-first when the Encore term ends or a role is signed.

Run **Sunday ~4pm**. Produces a saved markdown report plus the outputs below.

**Replaces:** `friday-cos-review` (retired May 2026). Mercy Ships finance role **concluded July 3, 2026** — all Mercy-Ships recurring items are retired from this skill.

---

## Track Separation — Read This First

| Track | Plugin | Skill | Cadence | Question |
|---|---|---|---|---|
| **Operational (mid-week)** | CoS plugin | `cos-sync` | Friday | "Is my task list clean and worked mid-week?" |
| **Strategic + weekly planning** | Weekly Review plugin | `weekly-review` (this) | Sunday 4pm | "Am I on the right things — and is the week ahead assigned?" |

Sunday and Friday are **both** operational, on purpose: Sunday **plans and assigns the week ahead**; Friday **cleans up and executes** mid-week. Different jobs, same task list.

**Two-button rhythm:** Jeff runs ONE button Sunday (this skill) and ONE button Friday (`cos-sync`).

**Global rule — enumerate options:** anywhere this review asks Jeff to defer, cut, reprioritize, or decide, present a **numbered list of concrete options**, not an open question.

---

## Source-of-Truth Rule

There is no "Jeff-now brief," and one must not be built. That slot was closed 2026-08-12 — the **Master Projects sheet is the current-state authority**.

| Layer | Holds | Maintained by |
|---|---|---|
| **Master Projects sheet** | Program state — status, milestone, health, next action | This review, weekly |
| Account-level global instructions | Identity, employment status, location, hard constraints, weekly rhythm, killed items | This review, **by exception only** (Step 11) |
| `[YOUR_PROJECTS_PATH]/.claude/CLAUDE.md` | Build/technical conventions | On change, not here |

**Never propose adding program status to a CLAUDE.md file or to the global instructions.**

---

## STEP 1 — Confirm Date and Collect Manual Inputs

Confirm today's full date. Sets the 8-day lookback and 14-day forward window.

Prompt:
> *"Any fixed commitments this week I should know about for scheduling? (meetings, travel, family — 2–3 lines, or skip)"*

Wait for the response before pulling data. **On an unattended scheduled run,** proceed using Google Calendar as the sole commitment source and say so explicitly in the report.

**Always check the week for public holidays and school holidays.** Recurring calendar events do not auto-cancel for holidays, so the calendar will show school drop-off on Labor Day. A holiday inside the week reshapes the whole plan — verify it before scheduling, do not infer it from the calendar.

---

## STEP 2 — Data Pull (run in parallel)

**Todoist:**
- Completed tasks — last 8 days
- **The `needtoschedule` queue** — the scheduling pool for Step 5b
- **If `needtoschedule` is too thin, dip into `someday`** to top up, and propose promotions for anything becoming important
- Upcoming tasks — next 14 days (already dated; for conflict avoidance)
- Return to Auburn project — full task list
- Encore Consulting project — full task list
- Tasks with label `#blog` or `%makethebotwork`
- All tasks in the AI Agents project

**Monthly full sweep (first Sunday of each month only):** pull *all* open tasks regardless of label.

**Master Projects sheet (Google Drive):** pull the program/goals layer (see auto-memory → *Master Projects Sheet*). Needed for the Program Alignment gate (Step 4).

**Apple Notes:** notes modified in the last 8 days from Voice Memo Reflections, Professional Development (+ subfolders), Family Shared. **Known bug: folder names containing `&` fail AppleScript lookup** (`Walk & Talk` cannot be read this way). If a folder cannot be resolved, say so as a gap — do not report "nothing found."

**Calendar:** events for the next 14 days.

No Notion pull.

---

## STEP 3 — Output 1: Weekly Recap

**Hard limit: 250 characters.** What happened, what mattered, one insight. One paragraph, no bullets.

---

## STEP 4 — Output 2: Program Alignment Gate

Compare where time actually went (completed tasks, Apple Notes) and the upcoming load against the **Master Projects sheet**.

- For each active program: on track / stalled / drifting?
- **Flag anything Jeff is spending time on that is NOT a tracked program.** For each, classify (numbered): (1) promote to a tracked program, (2) log it, (3) backlog or drop.
- Surface at most the 3 sharpest misalignments.

**Sheet maintenance — produce a paste-over file, not cell-by-cell edits.** The Drive MCP is read-only for Sheets. Write a **tab-separated `.tsv` file** containing every data row (headers excluded) so Jeff can paste the whole block in one action. Save to `[workspace]/weekly-review-plugin/YYYY-MM-DD-master-projects-paste-over.tsv`.

**Rules for the TSV:** one line per program, tabs between columns, **no newlines inside any cell** (they break the paste). Column order: Project Name · Category · Status · Core Outcome · Current Milestone · Todoist Project(s) · Health · CoS Notes · Last Updated.

Never merely report that a row is out of date. A flag without a fix gets re-flagged next week and nothing changes.

---

## STEP 5 — Output 3: Todoist Audit + Load Plan

**5a — Audit the pulled queue:**
- **Close dead tasks, do not delete them** — and **add a comment first** explaining why it is closing and what, if anything, carries forward. Jeff's standing preference (2026-09-06): context is preserved, not destroyed.
- Vague tasks: **date them for tomorrow and add a comment stating exactly what is needed to clarify them.** Do not just list them as vague.
- Apply priority (P1–P4) and AI-leverage labels (🤖 / 🤝 / 👤)
- Detect duplicates; flag project moves
- Propose promotions along `idea → makethebotwork → burntokens` where specs are complete
- **Guard the intake gate:** if `needtoschedule` has grown past ~40 items, say so plainly.

**5b — Load plan / schedule the week:**

**CAPACITY MODEL (corrected 2026-09-06 by Jeff — this replaces the old 8.5 h figure).**

Jeff's realistic total working day is **9–10 hours**, but that number **includes all the domestic load that never appears in Todoist**: childcare, cooking, cleaning, writing chore lists for the kids, and ad-hoc house work. Budget it explicitly:

| | School day | School holiday / no school |
|---|---|---|
| Total day | ~9.5 h | ~9.5 h |
| Unlogged domestic load | ~3.0 h | ~5.0 h |
| **Todoist-visible capacity** | **~6.5 h** | **~4.5 h** |

**Schedule against the Todoist-visible number, not the 9–10 h total.** Physical house work (fence, wall, shed) IS Todoist-visible and counts against it. Childcare and cooking are not and must never be scheduled over.

Revisit this model whenever household coverage changes.

**Other scheduling rules:**
- Working window and fixed family commitments (school drop-off and pickup times): `[YOUR_SCHEDULE]`.
- Exclude "BLOCK - DNB" / "Focus Time."
- **Priority weight: Encore → job search → house build → Auburn admin** (see the priorities block above).
- **Respect the weekly rhythm:** Mon–Tue take the heavy thinking; Wed suits the AI-leverage batch and email; Thu is lower-intensity; Fri stays light and family-weighted. Keep the back half loose while a live loop is running.
- **Leave 25–35% headroom.** A calendar with no meetings on it is not a free week — it is an unscheduled one, and the meetings land with no notice.
- **The `needtoschedule` label comes OFF the moment a task gets a date.**
- Day-by-day list with time estimates, running daily total vs. the cap, one-line verdict per day (*under / at / over*). Flag overflow with a **numbered** defer list.
- Time-estimate defaults: simple 30 min, medium 1 h, complex 2 h.
- **Check the arithmetic on every daily total before publishing.** Sum the minutes explicitly; do not eyeball them.

The load plan is persisted (Step 11) as the ceiling `cos-sync` reads Friday.

---

## STEP 6 — Output 4: App & AI Work Tracker

Scope: AI Agents project + `#blog` + `%makethebotwork`. Bucket each: **Claude-autonomous** / **Needs Jeff's decision** / **Requires Jeff's time**. Format: Item | Bucket | Next action. Flag anything waiting on a Jeff decision 7+ days.

**Standing gate (2026-08-23):** no WordPress, blog, or `makethebotwork` blog-rewrite task is proposed or scheduled until Voice Engine is fixed. Pure Voice Engine *infrastructure* tasks are exempt.

---

## STEP 7 — Output 5: Decision Accountability

**The log lives at `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/decision-log.md`.** It is a file, read AND written directly by CoS sessions — no MCP, no paste step. Append observed decisions in the file's own format.

The Apple Note this previously named was retired 2026-08-28 and could never be resolved by name. Do not go looking for it.

Surface:
- Logged decisions due for follow-up (>14 days, no follow-up entry)
- **Open Todoist tasks that contradict a logged decision** — this is the actual value of the log
- Silently abandoned decisions (>30 days, no action)

**Do not report "the log is N days stale."** Silence means no decisions were made, which is normal.

**Unlogged decisions:** scan the last 8 days of Apple Notes, completed tasks, and this session for decision-shaped language (chose / decided / going with / killed / committed) that is NOT in the log. For each, ask (numbered) whether to log it. Catching un-logged decisions is the higher-value half.

---

## STEP 8 — Output 6: Horizon Scan (2-week lookahead)

Scan the next 14 days across calendar, Todoist due dates and deadlines, and the current priorities.

**Check every run:**
- **Todoist `deadlineDate` fields, not just due dates.** A task can be due in two weeks and have a hard deadline this week — that combination hides.
- **Encore gates** — Gate 1 (question set approval, W4), Gate 2, invoice dates, and the 2026-11-28 term end.
- **Job search** — interview loops, follow-ups, references. Flag anything time-sensitive with no next action.
- **Auburn admin close-out** — flag any item with a hard date and no active task.
- **An empty calendar is itself a finding.** If there are live processes and no meetings booked, say so.

Format (max 6 lines): 🔴 ≤7 days, no task/prep · 🟠 8–14 days, prep now · 🟡 known horizon item with no active task.

Reads the horizon; does not act.

---

## STEP 9 — Output 7: Auburn Pulse (brief)

The physical move is complete; this tracks remaining admin. 3–5 lines:
- 🔴 anything due within 30 days
- 🟠 any [PARTNER]-owned item with no movement in 14+ days. **Read `responsibleUid`, not `assignedByUid`** — confusing the two has produced a false "[PARTNER] stalled" flag before. Jeff = [YOUR_TODOIST_UID], [PARTNER] = [PARTNER_TODOIST_UID].
- 🟡 count of critical no-date items

If all clear, one line. Full analysis lives in `ca-move-pulse`.

---

## STEP 10 — Output 8: Config Integrity Check

**Why this exists:** on 2026-08-12 two config entries were found pointing at skills that do not exist. A skill trigger naming a missing skill does not error; it silently does nothing.

**Run every week. Should take one line when clean.**

1. **Collect referenced names** from `[YOUR_PROJECTS_PATH]/.claude/CLAUDE.md`, `[YOUR_PROJECTS_PATH]/Professional Development/CLAUDE.md`, `[YOUR_PROJECTS_PATH]/AI_RULES.md`, and this skill plus `cos-sync`.
2. **Compare against reality.** Check each referenced skill against the installed list, each path exists, the sheet ID resolves.
3. **Report only mismatches, with a paste-ready fix** — the corrected line, not a note that something is broken.
4. **Report MCP servers that failed to connect or need authorization**, and name what output was lost as a result. Do not post to Slack; the review is delivered only as the saved report and the in-session summary.
5. If everything resolves: *"Config integrity clean — N skill references, M paths, all resolve."*

**Known blind spot:** the account-level global instructions are **not readable from disk**. This check cannot verify skill references inside them. Say so once in the output so the gap stays visible.

**Editing this skill:** on-disk skill files in Cowork are a read-only cache. Use the `save_skill` tool with `overwrite: true`.

---

## STEP 11 — Phase Check, Persist Memory, Save Report

**Phase check (by exception only):** compare the *Current Phase* block in the account-level global instructions against what this review found. Only these live there: employment status, location, hard constraints, weekly rhythm, conventions in force, killed/parked items.

If any changed, **output the replacement block as paste-ready text**. If nothing changed, say *"Phase block current."*

**Memory pass:** ask *"Anything from this review worth persisting to memory?"*

**Save report:** `[workspace]/weekly-review-plugin/reports/weekly-report-YYYY-MM-DD-HH-MM-SS.md`, where `[workspace]` = `[YOUR_PROJECTS_PATH]/Professional Development/`.

**Save load-plan artifact:** `[workspace]/weekly-review-plugin/load-plan-current.md`. **Copy the prior version to `reports/load-plan-YYYY-MM-DD-archived.md` before overwriting.** Date-stamp inside.

**Save sheet paste-over:** `[workspace]/weekly-review-plugin/YYYY-MM-DD-master-projects-paste-over.tsv` (Step 4).

Confirm all paths to the user.

---

## Known Limitations
- **Job search pipeline:** no dedicated skill, deliberately. The constraint is pipeline *volume*, not tracking.
- **Relationship-debt tracking:** deferred → revisit ~October 2026.
- **Apple Notes MCP:** date filtering is manual; folder names with `&` cannot be resolved.
- **Master Projects sheet:** Drive MCP is read-only — produce the TSV paste-over.
- **CLAUDE.md files:** `[YOUR_PROJECTS_PATH]/Professional Development/CLAUDE.md` is writable from Cowork. `[YOUR_PROJECTS_PATH]/.claude/CLAUDE.md` may not be — propose paste-ready text if a write fails.
- **Global instructions:** account-level, not on disk. Cannot be read or verified from a session.
- **Slack: never post.** As of v2.8 the weekly review does not send a Slack summary and does not draft one. Skip any earlier instruction to do so.

---

## Do Not Reintroduce
- **Reschedule-count detector** — killed 2026-07-15. Produced stress without value.
- **"Jeff-now brief"** — closed 2026-08-12. The sheet is the current-state authority.
- **Self-ruleout coaching** — closed. Overruled twice, deliberately.
- **Vercel commute / anchor days** — closed 2026-08-14, not deferred. Never solicit.

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-18 | v1.0 — created. Replaced friday-cos-review. |
| 2026-06-03 | v2.0 — Compass plugin. decision-accountability section. Memory-persist pass. |
| 2026-06-05 | v2.1 — Standalone plugin. Horizon Scan. Load-plan handoff. Calendar pull. |
| 2026-06-29 | v2.2 — Removed Asana Cross-Functional Tracker. |
| 2026-07-15 | v2.3 — Removed Reschedule Detector. |
| 2026-07-16 | v2.4 — Moved to Sunday 4pm. Retired Mercy Ships. Added Program Alignment gate and the operational Todoist audit. |
| 2026-08-12 | v2.5 — Step 2 pulls `needtoschedule`; monthly full sweep; Source-of-Truth rule; paste-ready values. |
| 2026-08-12 | v2.6 — Added Step 10 Config Integrity Check. |
| 2026-09-06 | v2.7 — **Capacity model rewritten**: 9–10 h total day *including* unlogged domestic load, yielding ~6.5 h Todoist-visible on a school day and ~4.5 h on a holiday. Replaces the wrong 8.5 h figure. **Priority order changed to Encore-first** by Jeff's ruling. **Decision-log path corrected** to `cos-plugin/decision-log.md` (Step 7). **Sheet maintenance now produces a TSV paste-over file** instead of cell-by-cell values. Added: holiday check in Step 1, dead-task handling changed from delete to close-with-comment, vague tasks get dated + commented, `deadlineDate` check in the Horizon Scan, empty-calendar-is-a-finding rule, MCP-failure reporting in Step 10, arithmetic-verification rule in Step 5b, load-plan archiving before overwrite, and the Apple Notes `&` folder bug. |
| 2026-10-12 | v2.8 — **Slack removed**: the review no longer posts or drafts a Slack summary. Output is the saved report and the in-session summary only. |

