---
name: friday-cos-review
description: Run a structured Friday afternoon Chief of Staff review session. Pulls Todoist tasks and Notion project synopses, asks for manual context, then produces three outputs — a weekly CoS review, a team email draft, and an open-threads triage. Trigger on "Friday CoS," "weekly review," "run my CoS skill," or when the user signals it's time for their Friday session.
version: "2.0"
---

# Friday Afternoon Chief of Staff Skill

<!--
CUSTOMIZATION GUIDE
This skill is a template. Replace all [PLACEHOLDER] values before use.

Required substitutions:
- [YOUR ORGANIZATION]: Your employer or primary work context
- [YOUR TEAM]: The people who receive your weekly email (direct reports or close colleagues)
- [YOUR MONTHLY RECIPIENTS]: Expanded list for first-Friday-of-month updates
- [YOUR PARTNER]: Your partner or spouse (for the life/family tracking section)
- [YOUR DESTINATION]: Where you're moving or what major life project you're tracking
- [YOUR RELOCATION PROJECT]: The Todoist project tracking your major life event
- [YOUR NOTION DB]: The Notion database where weekly project synopses are stored
- [YOUR ACTIVE PROJECTS]: The 2–3 Claude projects you run synopsis prompts for
- [YOUR STORAGE PATH]: Where in Apple Notes (or your note-taking app) the weekly review saves

Delete the CA Move Pulse section (Step 5) if you don't have an active relocation or
major life project to track. Or adapt it to whatever parallel project applies.
-->

---

## What This Skill Is

A structured Friday afternoon session in Claude Pro that replaces manual weekly review, email drafting, and AI chat triage. It runs once per week and produces three outputs. Total time investment: 45–60 minutes including your review and edits.

---

## What You Need Before Starting

- Todoist MCP connected (Settings → Connectors → https://ai.todoist.net/mcp)
- Notion MCP connected (Settings → Connectors → https://mcp.notion.com/mcp)
- **Pre-work complete:** Weekly Project Synopsis prompts already run for each active Claude project and synced to Notion "[YOUR NOTION DB]" database (do this before starting this session)
- 5 minutes for pre-session triage (see Pre-Work below)
- 2–3 sentences ready about your recurring [YOUR ORGANIZATION] week
- 2–3 sentences ready about your family week (until Apple Notes integration is live)

---

## The Three Outputs + Standing Section

| # | Output | Audience | Saved To |
|---|--------|----------|----------|
| 1 | CoS Weekly Review (incl. [YOUR MAJOR LIFE PROJECT] Pulse) | You | [YOUR STORAGE PATH] → YYYY-MM-DD |
| 2 | Weekly Email Draft | [YOUR TEAM] (or expanded list if first Friday) | You review, edit, send |
| 3 | Open Claude Threads Review | You | Todoist tasks created in-session |

---

## PRE-WORK — Run Before Opening This Session

Do this before starting the Friday CoS session, in this order:

**1. Todoist triage (5 min)**
Go through your active task list. Close tasks that are done but unchecked. Complete any task that's no longer relevant (add comment: "Closing — no longer relevant"). Do not reschedule or reorganize — just clean up.

**2. Notion synopsis prompts (15–20 min)**
For each active Claude project, run the Weekly Project Synopsis prompt. This produces a structured page in your Notion "[YOUR NOTION DB]" database covering Highlights, Lowlights, Learnings, Projects, and Reflections for that project. The Friday CoS Skill reads these pages — they must exist before you start.

Active projects to run synopsis for (update this list as projects change):
- [YOUR ACTIVE PROJECT 1]
- [YOUR ACTIVE PROJECT 2]
- Any other active Claude project with sessions in the last 8 days

**3. Confirm Notion pages exist** before proceeding. If a synopsis page is missing for an active project, either run the prompt now or note it as a gap — Claude will flag it in the Slippage Report.

---

## STEP 1 — Start the Session

Open a fresh Claude Pro conversation. Paste this exactly:

---

> Run my Friday CoS Skill. Today is [INSERT DATE — e.g., Friday, April 10, 2026].
>
> Please do the following in order, pausing after each output for my review:
> 1. Pull my Todoist data (completed tasks — last 8 days, upcoming tasks — next 10 days)
> 2. Pull this week's Notion project synopsis pages from the "[YOUR NOTION DB]" database
> 3. Review my recent Claude conversations (last 10 days)
> 4. Ask me for my manual context before producing any output
> 5. Produce Output 1: CoS Weekly Review
> 6. Wait for my review and edits, then produce Output 2: Weekly Email
> 7. Wait for my review and edits, then produce Output 3: Open Claude Threads Review

---

### What Claude Will Do Next

Claude will:
1. Pull your Todoist completed tasks (8-day window: last Friday through today)
2. Pull your upcoming tasks (next 10 days) — *this requires one approval confirmation at session start*
3. Search Notion "[YOUR NOTION DB]" database for pages with Date = this week. Read each page in full.
4. Pull your Claude conversation history from the last 10 days across all projects
5. Ask you for your manual context block (see Step 2)

**If Notion pages are missing:** Claude flags which projects have no synopsis page for this week and notes this in the Slippage Report. The session continues — a missing synopsis does not halt the workflow.

---

## STEP 2 — Provide Your Manual Context Block

Claude will prompt you with three questions. Answer in a few sentences each.

**[YOUR ORGANIZATION] recurring work:**
*"How did the week go on your recurring [YOUR ORGANIZATION] work — team meetings, key decisions, strategic initiatives? Any notable outcomes, issues, or wins?"*

Answer honestly. If something was late or missed, say so. If a meeting surfaced something important, include it. This is the data that won't show up in Todoist or Notion.

**Family context:**
*"What's worth noting from the family side this week?"*

Feeds the CoS review only — not the email. This prompt will be retired once Apple Notes integration is live.

**Apple Notes and personal reflections:**
*"Do you have anything from your Apple Notes or journal this week worth including — leadership observations, spiritual reflections, or personal learnings?"*

Paste or summarize anything worth capturing. This is the heart-level data no automated system will surface.

---

## STEP 3 — Output 1: CoS Weekly Review

Claude synthesizes all four data sources into the review. You review before anything else is generated.

### Data Sources and Their Roles

| Source | What it contributes |
|--------|---------------------|
| **Todoist** | Task completion, slippage, upcoming load, [YOUR MAJOR LIFE PROJECT] status |
| **Notion: Weekly Project Synopses** | Project-level highlights, lowlights, learnings, and builds — pre-synthesized by project |
| **Claude conversation history** | Open threads, work-in-progress, accomplishments worth logging |
| **Manual context block** | Recurring [YOUR ORGANIZATION] work, family updates, Apple Notes reflections not captured elsewhere |

When Notion synopsis data and Todoist data conflict (e.g., Notion marks something complete but Todoist still shows it open), Claude flags the discrepancy — it does not silently resolve it.

---

### Structure

**Header**
`Weekly CoS Review — [DATE RANGE, e.g., Apr 3 – Apr 10, 2026]`

---

**Execution Score by Context**

Contexts listed in order of most active to least active this week. Each rated High / Medium / Low with one sentence of evidence. Source cited where not obvious.

Example:
- Career/Professional — High: Three priorities completed (Todoist) and [PROJECT NAME] reached a milestone (Notion).
- [YOUR ORGANIZATION] — Medium: [RECURRING WORK TYPE] completed (manual context); [OPEN ITEM] still open (Todoist).
- Family — Low: One [YOUR MAJOR LIFE PROJECT] task completed (Todoist); [CATEGORY] items remain unactioned.

---

**Accomplishments That Actually Matter**

Not a task list. Meaningful progress only — things that advanced a goal, changed a situation, or produced something real. Draw from Notion synopses first (pre-filtered for significance), then supplement with Todoist completions and Claude thread history. Maintenance tasks excluded.

---

**Learnings and Insights**

Pulled from Section 3 of each Notion project synopsis page, plus anything from your Apple Notes or manual context block. Deduplicate across projects — if the same insight appears in two synopsis pages, merge and note both sources.

Format: bullet points. Lead each bullet with domain tag: [AI/Automation], [Finance], [Leadership], [Career], [Systems/Workflow], [Family/Personal], [Other: specify].

Six-month filter applied: only include learnings worth remembering in six months or that would change how you approach a similar problem.

---

**Slippage Report**

Two tiers:

*This week's slippage* — tasks due within the 8-day window and not completed. Cross-reference with Notion synopses: if a synopsis lists something as a Lowlight or Blocker, include it here even without a Todoist due date.

*Older open items worth flagging* — uncompleted tasks older than the current window, flagged for avoidance or unclear ownership.

Missing Notion synopses are logged here as a process slippage item, not silently ignored.

---

**Overcommitment Assessment**

Four inputs:
1. Time estimates attached to tasks in Todoist
2. Task count and complexity
3. Manual context about recurring [YOUR ORGANIZATION] work
4. Project activity volume from Notion synopses (high Notion output signals high cognitive load even when Todoist looks clean)

Verdict: appropriately loaded / moderately overcommitted / significantly overcommitted — one paragraph. Judgment call, not a calculation.

---

**Top 5 Next Week**

Force-ranked. Weighted by life context. In transition periods or deadline-heavy windows, this will skew toward whichever context has the most at stake.

For each item: the task, why it's ranked where it is, one line on what "done" looks like.

Explicit call-out: anything NOT on the list you might be tempted to work on. Named by name.

---

**One Behavioral Call-Out**

One pattern — positive or corrective. Corrective is the default. Positive only when genuinely earned and specific. Draw from Notion's Leadership Doctrine reflection sections where available — your own observations carry more weight than anything Claude infers from task data.

Direct, blunt, short. One paragraph maximum.

---

### After You Review Output 1

Make corrections or additions in the chat. Then tell Claude: *"Output 1 approved — proceed to the email."*

Save the final version to [YOUR STORAGE PATH] → [YYYY-MM-DD].

---

## STEP 4 — Output 2: Weekly Email

Claude first determines which email type is needed by calculating today's date:

- **First Friday of the month** → Monthly stakeholder update (broader format, expanded recipients)
- **Any other Friday** → Weekly snapshot (to [YOUR TEAM] only)

---

### Weekly Snapshot (Most Fridays)

**To:** [YOUR TEAM MEMBER 1], [YOUR TEAM MEMBER 2], [YOUR TEAM MEMBER 3]
**Subject:** `Weekly snapshot - YYYY-MM-DD`
**Target length:** 200–300 words
**Tone:** [DESCRIBE YOUR PREFERRED TONE — e.g., "Warm but direct. Mix of operational and human. Written as a thoughtful leader at [YOUR ORGANIZATION], not a corporate status update."]
**Content:** [YOUR ORGANIZATION] only — no career, family, or personal content.

**Format:**

- Opening line (brief, casual — can note something special about the week)
- **One Big Thing** — one compelling theme or narrative. Not just a task. Something meaningful: strategic, relational, operational, or human.
- **Highlights** — 2–4 bullet points of wins or notable progress
- **Insights** — 1–3 bullet points with data, observations, or learnings
- **Blockers** — items where you are completely stopped. Prefix: `(Blocker)`
- **Watchpoints** — not problems today but could become problems. Prefix: `(Watchpoint)`
- **Next** — 2–4 items you are working toward next week

**One Big Thing — How Claude handles this:**

Claude will offer 3 angle options with a clear recommendation on which is strongest. You choose. Example:

> *Option A (Recommended): [DESCRIBE ANGLE A — most significant thing that happened]*
>
> *Option B: [DESCRIBE ANGLE B — a human story from the week]*
>
> *Option C: [DESCRIBE ANGLE C — a forward-looking theme]*

---

### Monthly Stakeholder Update (First Friday of Month)

**To:** [YOUR MONTHLY RECIPIENTS — e.g., leadership team, cross-functional stakeholders]

**Subject:** `Monthly Update - YYYY-MM-DD`

**Format:**
- Opening: "Hello!" + "Here is your [Month] update:"
- **[SECTION 1: YOUR DOMAIN — e.g., Team Experience, Customer Experience, Product Updates]** — numbered items
- **Team** — personnel movements, arrivals, departures, recognitions, transitions. Warm, personal language.
- **Optimizations** — process improvements, system migrations, automation wins. Use ✅ where complete.
- **Next** — numbered items of what's coming
- Closing: "Happy weekend!"

---

### After You Review Output 2

Edit in chat or copy to your email client. Tell Claude: *"Output 2 approved — proceed to the thread review."*

---

## STEP 5 — [YOUR MAJOR LIFE PROJECT] Pulse

<!--
This section tracks a parallel major life or work project alongside your weekly review.
Adapt it to whatever project needs a standing weekly check-in — a relocation, a job search,
a major initiative, a health goal. If nothing applies, delete this section.
-->

Standing section appended to Output 1 automatically. Produced from the [YOUR RELOCATION PROJECT] Todoist project.

**Upcoming deadlines (next 30 days)**
Tasks with due dates in the next 30 days, sorted by urgency. Includes owner. One line per item.

**No-date items in long-lead categories**
[LIST YOUR LONG-LEAD CATEGORIES — e.g., insurance, licensing, shipping, school enrollment, utilities activation]. Flagged as: *"Needs a date before [YOUR DEADLINE]."*

**[YOUR PARTNER] stall check**
Any task assigned to [YOUR PARTNER] with no completion in the past 14 days, flagged by name. Not criticism — conversation prompt.

### Format

```
[YOUR MAJOR LIFE PROJECT] Pulse — [DATE]

🔴 Due in next 30 days
- [task] — due [date] — @you/@partner

🟡 No date, needs one before [YOUR DEADLINE]
- [task] — category: [category]

🟠 [YOUR PARTNER] items with no recent movement
- [task] — last activity: [date]
```

If all three sections are clean, Claude says so in one line and moves on.

---

## STEP 6 — Output 3: Open Claude Threads Review

Claude pulls your last 10 days of Claude conversations and classifies each one. You do not scan anything — Claude does the first pass.

Conversations already captured in a Notion project synopsis are labeled **(Synced to Notion)** and deprioritized. Focus time on threads not covered by a synopsis.

### Three Categories

**Likely Open** — needs your attention
- Multi-exchange, iterative work (proposals, analysis, strategy, mapping, frameworks, decks)
- Created more than 24 hours ago but recently updated
- NOT already captured in a Notion project synopsis this week

**Probably Done** — no action needed
- Quick Q&A, single-exchange, factual lookups
- Already covered by a Notion synopsis (labeled as such)

**Worth Logging as an Accomplishment**
- Conversation complete, but work was significant enough for a Todoist accomplishment entry or career positioning note

---

### What Claude Presents

**Likely Open:** title, date range, one-sentence assessment of why it may still be open. Flag if it overlaps with a Notion synopsis.

**Probably Done:** title and date only. Note "(Synced to Notion)" where applicable.

**Worth Logging:** title, date, suggested Todoist task or accomplishment label.

---

### Your Decision for Each "Likely Open" Item

Claude asks only: *"What needs to happen next — does Claude need to do something, or do you need to do something?"*

Claude drafts a Todoist task: `[Action verb] + [topic]`

Examples:
- "Continue value stream mapping exercise with Claude"
- "Finish reviewing AI tool recommendations"
- "Finalize blog post scheduling decision"

Claude creates the tasks in Todoist directly.

---

### Session Goal

5–10 minutes. Default to skipping uncertain items. High-signal list, not exhaustive capture.

---

## STEP 7 — Session Close

Before ending the session, Claude confirms:
- Output 1 saved to [YOUR STORAGE PATH] (you confirm)
- Output 2 ready to send (you confirm)
- Output 3 tasks created in Todoist (Claude confirms count)
- Notion synopsis pages read this session: [list project names] (Claude confirms)

Total elapsed time target: 45–60 minutes.

---

## Known Limitations

- **Apple Notes integration:** Claude cannot yet read Apple Notes directly. Family context and personal reflections remain manual until this changes.
- **Notion dependency:** This Skill assumes the Weekly Project Synopsis prompts have been run before the session. If synopses are missing, Claude flags the gap and continues.
- **Upcoming tasks approval:** The `find-tasks-by-date` tool requires one confirmation at session start. One-line step — Claude will prompt you.
- **Recurring work:** Still requires manual context block. As more recurring tasks are added to Todoist, this block will shrink.
- **First Friday detection:** Claude calculates this from the date you provide. Always include the full date in your opening prompt.
- **Cross-project conversation scope:** When running this Skill in a specific Claude project, the recent_chats tool sees only conversations in that project. Notion synopses compensate for this by providing cross-project synthesis upstream.

---

## Changelog

| Version | Change |
|---------|--------|
| 1.0 | Initial release — Todoist integration, weekly review, email draft, thread triage |
| 2.0 | Added Notion project synopsis integration; added personal reflections prompt to manual context block; added Learnings and Insights section; updated Overcommitment Assessment to four inputs; added Notion sync labeling in Output 3; added Pre-Work section |
