---
name: ca-move-pulse
description: "Pull the Return to Auburn Todoist project and produce two outputs: (1) a 5–6 line Slack #automate brief, (2) a full CA Move Pulse block for inclusion in the CoS Weekly Review. Trigger when the user says 'Run CA Move Pulse', 'Move pulse', 'Auburn pulse', or when called automatically from the Friday CoS skill. Also run standalone any time Jeff wants a point-in-time read on the Auburn move."
---

# CA Move Pulse Skill
### Version 1.0 — May 2026
### Platform: Claude Cowork + Todoist MCP

---

## What This Skill Does

A focused, repeatable session that pulls the **Return to Auburn** Todoist project and categorizes all open tasks by urgency relative to the July 3, 2026 hard departure date. Produces two outputs in sequence:

- **Output 1 (Slack brief):** 5–6 lines, urgent items only, suitable for posting to #automate
- **Output 2 (CoS block):** Full formatted CA Move Pulse section, ready to paste into the CoS Weekly Review

Total run time: under 5 minutes. No manual context block required.

---

## Before Starting — Confirm Date

Always confirm the full session date before pulling data. Needed to:
- Calculate days remaining until July 3 departure
- Identify which tasks are due within the next 30 days
- Apply the 14-day Jaclyn stall check

**Formula:** July 3 is the hard departure date. Days remaining = July 3 minus today.

---

## Key Constants

| Constant | Value |
|----------|-------|
| Todoist Project ID | `6R7vHcH76rMWPcc5` |
| Hard departure date | July 3, 2026 |
| Jeff's Todoist UID | `14075501` |
| Jaclyn's Todoist UID | `16889336` |
| Unassigned tasks default to | Jeff |
| High-risk undated categories | insurance (health, homeowners, vehicle), licensing (CPA, PT), school enrollment, shipping, utilities activation, tenant walkthrough |
| Jaclyn stall threshold | 14 days since last activity |

---

## STEP 1 — Pull Return to Auburn Project

Pull all open tasks from project `6R7vHcH76rMWPcc5` with `responsibleUserFiltering: all` and `limit: 100`.

No user confirmation required. Pull silently and proceed.

---

## STEP 2 — Categorize Tasks

Sort all open tasks into four buckets:

### 🔴 Due in next 30 days
- Tasks with a due date within 30 days of today's session date
- Sort by urgency: soonest due first
- Include assignee (@jeff / @jaclyn). Unassigned = @jeff.
- Include due date

### 🔴 Undated — high-risk categories
Tasks with **no due date** that fall into any of these categories:
- Health insurance
- Homeowners insurance
- Vehicle insurance
- CPA license / PT license (licensing)
- School enrollment
- Shipping from abroad
- Utilities activation
- Tenant walkthrough / move-in inspection

Label each: `— needs date before July 3`

### 🟡 Undated — all other tasks
Tasks with no due date, not in a high-risk category. List task name and category (infer from section or content). No urgency label needed.

### 🟠 Jaclyn items with no recent movement
Tasks assigned to Jaclyn (`responsibleUid: 16889336`) that are open. Flag all of them — Todoist MCP does not surface last-activity dates natively, so flag any open Jaclyn task as a coordination prompt.

**Framing rule:** This is a coordination prompt, not criticism. Phrase flags as "loop in Jaclyn" not "Jaclyn hasn't acted."

---

## STEP 3 — Clean Section Rule

If any of the four sections has zero items, replace it with a one-line clean status:
- "🔴 Due in 30 days — clear"
- "🔴 High-risk undated — clear"
- "🟡 Undated — clear"
- "🟠 Jaclyn items — none flagged"

If **all** sections are clean, output one line: "CA Move Pulse — all clear. 63 days to July 3." (with days calculated dynamically) and skip both outputs.

---

## STEP 4 — Output 1: Slack Brief

**Format:** Plain text, 5–6 lines max. Urgent items only (🔴 sections). No 🟡 or 🟠 in the Slack brief unless they are zero and you want to note it.

```
CA Move Pulse — [DATE] ([N] days to July 3)

🔴 Due soon:
- [task] — [due date] — @jeff/@jaclyn

🔴 High-risk, no date:
- [task] — [category]

[One line if anything is clean or if Jaclyn has items that need coordination]
```

**Character target:** Under 400 characters so it renders cleanly in Slack without truncation.

---

## STEP 5 — Output 2: CoS Block

**Format:** Full formatted block, ready to paste directly into the CoS Weekly Review after the Behavioral Call-Out section.

```
---
CA Move Pulse — [DATE] | [N] days to July 3

🔴 Due in next 30 days
- [task] — due [date] — @jeff
- [task] — due [date] — @jaclyn

🔴 Undated — high-risk (needs date before July 3)
- [task] — category: [insurance / licensing / shipping / enrollment / utilities / walkthrough]

🟡 Undated — all other
- [task] — [inferred category]

🟠 Jaclyn items — loop in
- [task] — open, no recent activity

[If any section is clean, replace with one-line clean status per Step 3]
---
```

---

## Known Limitations (May 2026)

- **No last-activity date from Todoist MCP:** The find-tasks tool does not return last-modified timestamps. Jaclyn stall detection flags all open Jaclyn tasks, not just stalled ones. Until Todoist surfaces activity dates, treat all Jaclyn-assigned open tasks as needing a coordination check.
- **Subtask visibility:** Parent tasks and subtasks are returned together. When a parent task has no date but subtasks do, use the earliest subtask date as a proxy. When in doubt, surface the parent.
- **Section names not surfaced in output:** Tasks are categorized by content and description inference, not section name. High-risk category classification is pattern-matched from task content. Edge cases may require manual review.
- **Assignment inference:** Unassigned tasks (no `responsibleUid`) default to Jeff. This is correct for sole tasks but may be ambiguous for family decisions. Flag any task that reads as a joint decision with "— @jeff (confirm Jaclyn alignment)".
- **Todoist pagination:** If `hasMore: true` is returned, make a second call with the cursor before proceeding. The project currently has ~95 tasks and fits in one call.

---

## Integration with Friday CoS Skill

The CA Move Pulse is called automatically at the end of Output 1 in the Friday CoS skill. When run inline:
- Skip STEP 1 pull (data already in context from CoS Step 1 pull)
- Skip Output 1 (Slack brief not produced inline — CoS is a document session, not Slack)
- Produce Output 2 only, appended directly after the Behavioral Call-Out

When run standalone, produce both outputs in sequence.

---

## Session Trigger Phrases

Run this skill when the user says any of:
- "Run CA Move Pulse"
- "Move pulse"
- "Auburn pulse"
- "How are we tracking on the move?"
- "Where are we on Auburn?"
- Called automatically by `friday-cos-review` skill

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-01 | v1.0 — created in Claude Cowork |
