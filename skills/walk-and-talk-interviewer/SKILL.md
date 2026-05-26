---
name: walk-and-talk-interviewer
description: >
  Interview Jeff about his vague, stalled, or unclear Todoist tasks to extract real context and intent — then write that context back into Todoist as improved descriptions, subtasks, and notes. Use this skill when Jeff says "walk and talk", "interview me about my tasks", "let's do a task interview", "I went for a walk", "I have a transcript", "help me clarify my tasks", "run the interviewer", or when there's a voice transcript pasted or available in the CoS — Walk & Talk Apple Notes folder. Supports two modes: Walk & Talk (Jeff answers a batch of questions on a walk and pastes/drops the transcript) and In-Session (quick inline Q&A for 1–3 tasks). Always trigger this skill when the user has a voice memo or transcript and wants to connect it to their Todoist.
---

# Walk & Talk Interviewer
### Version 1.2 — May 2026
### Part of: CoS Plugin
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP

---

## What This Skill Does

Turns vague, stalled, and ambiguous Todoist tasks into actionable, well-defined work items — by interviewing Jeff rather than guessing.

Two modes:

| Mode | When to use | How answers come in |
|------|-------------|---------------------|
| **Walk & Talk** | Many tasks need clarification, or Jeff wants to think out loud | Jeff goes for a walk, records answers to a batch of questions, pastes the transcript (or you pull it from Apple Notes) |
| **In-Session** | 1–5 tasks, quick inline Q&A | You ask questions directly in the Cowork window, Jeff types answers |

Both modes produce the same output: clarified Todoist tasks with updated descriptions, subtasks where appropriate, and a session summary.

---

## Key Constants

| Constant | Value |
|----------|-------|
| Apple Notes folder for transcripts | `Professional Development → Voice Memo Reflections → Walk & Talk` |
| Jeff's Todoist UID | `14075501` |
| Stall threshold (days without activity) | 14 days |
| Max tasks to interview in one Walk & Talk session | 10 (to keep the walk under 30 min) |
| Workspace report path | `/Users/jeffbeaumont/Projects/Professional Development/cos-plugin/reports/` |

---

## STEP 1 — Determine Mode

Ask Jeff which mode he wants **only if it isn't already clear from context**:

- If he pastes a transcript, drops a note, or says "I just got back from a walk" → **Walk & Talk mode**, skip to Step 3 (transcript processing)
- If he says "interview me", "walk and talk", or "let's go through tasks" without a transcript → **Walk & Talk mode, pre-walk phase** — pull Todoist + Apple Notes first (Step 2), generate the interview script, send Jeff on the walk
- If he says "just a couple tasks" or names 1–3 specific tasks → **In-Session mode** — pull those tasks only, ask questions inline
- If Apple Notes MCP is connected, check the `Walk & Talk` folder (under `Professional Development → Voice Memo Reflections`) for any note modified in the last 24 hours before asking — if found, go directly to transcript processing

**The full Walk & Talk flow is:**
1. Pull Todoist + Apple Notes (Step 2) → read everything first
2. Generate interview script (Step 3) → send Jeff on the walk
3. Jeff records, saves transcript to Apple Notes
4. Pull transcript or accept paste (Step 3 return)
5. Process transcript (Step 4) → write back to Todoist (Step 5)
6. Hand off vague/unclear tasks to Todoist Organizer

---

## STEP 2 — Pull Todoist + Apple Notes

Pull both data sources before generating the interview script. Reading everything first is what makes the questions non-generic — questions should be grounded in what's actually in Jeff's system, not invented.

### Apple Notes pull

Pull all notes modified in the last 8 days from these folders (in priority order):
1. `Professional Development` (and all subfolders, including `Voice Memo Reflections`)
2. `Family Shared` (lower weight — include only if substantive)

Read these for: context on projects Jeff is thinking about, ideas he's captured, blockers he's mentioned, anything that illuminates a vague Todoist task.

If Apple Notes MCP is not connected, note the gap and proceed with Todoist only.

### Todoist pull

Pull Jeff's full active task list. Two windows to scan:

**Window A — Overdue and stalled**
- Task name is vague (one phrase with no action verb: "Marketing stuff", "Think about X", "Q3 planning")
- No description and no subtasks on a complex-sounding task
- Due date is overdue by 7+ days with no apparent blockers noted
- Task has been in Todoist 14+ days with no completion, subtask, or comment activity (use creation date as proxy if last-activity isn't surfaced)
- Task is in a project that's been stalled (no completions in 14 days)

**Window B — Upcoming in the next 10 days**
Pull all tasks due within the next 10 days. From that list, flag any that are:
- Vague or undefined (no description, no clear deliverable implied by the task name)
- Complex-sounding with no subtasks (suggesting no one has thought through the steps yet)
- Likely to need external coordination or research before they can be executed

The 10-day window is calculated from the session date, not the calendar week. This means a Friday session covers the weekend plus the full following week; a Monday session covers that week plus the following Monday through Wednesday.

**AI Leverage Assessment — run for every flagged task**

Before generating interview questions, assess each flagged task for AI leverage:

> Can Claude do 90%+ of the work on this task with minimal input from Jeff?

Apply this label to each task:

| Label | Meaning |
|-------|---------|
| 🤖 **Claude can lead** | Claude can produce a full draft, proposal, research output, or working artifact — Jeff reviews and approves. Minimal interview needed; just a directional answer. |
| 🤝 **Claude can assist** | Claude can take the first 50–70% (draft, framework, options list), but Jeff needs to provide domain judgment or a decision. Ask focused questions. |
| 👤 **Jeff must lead** | Primarily judgment, relationship, or execution that can't be delegated. Claude can prep and support but not drive. Ask clarifying questions to unblock. |

Examples of 🤖 Claude can lead:
- Research tasks ("Look into X vendors", "Summarize Y policy")
- First-draft writing (proposals, emails, strategy docs, SOPs)
- Building or updating a widget, app, script, or automation
- Generating a prompt for Claude Code to act on
- Comparison matrices, decision frameworks, option analyses
- Todoist task breakdowns and subtask generation

Surface the AI leverage label alongside each task in the interview script. For 🤖 tasks, generate a proposed first action Claude will take immediately after Jeff gives a directional answer — don't wait for a full transcript to start.

**Cap:** Surface the top 10 candidates maximum across both windows. Prioritize by: overdue → upcoming in next 3 days → upcoming in days 4–10 → stalled.

Do not surface tasks that are clearly self-explanatory single actions (e.g., "Buy stamps", "Reply to Sarah re: invoice").

---

## STEP 3 — Generate Interview Questions

For each flagged task, generate 2–4 targeted questions. The goal is to extract:

1. **What does "done" actually look like?** (The deliverable, not the activity)
2. **What's blocking it, if anything?** (Dependency, decision, information gap, or just hasn't started)
3. **What's the next single physical action?** (The thing Jeff would do if he sat down right now)
4. **Who else is involved, if anyone?**

**Question design rules:**
- Never ask questions whose answers are already obvious from context (don't ask "what project is this for?" if it's in the Return to Auburn project)
- Lead with the most clarifying question first — if Jeff only answers one, it should be the one that unlocks the most
- For overdue tasks, always ask: "Is this still relevant, or should we kill it?"
- Phrase questions in second person, conversationally — Jeff will be reading these aloud on a walk

### Walk & Talk batch format

Compile all questions into one numbered interview script. Group by task. Include the AI leverage label for each task so Jeff knows what to expect when he gets back. Format for reading aloud:

```
Walk & Talk Interview — [DATE]
[N] tasks, ~[N×3] minutes

---

TASK 1: "[Task name]" (Project: [Project]) — 🤖 Claude can lead
One sentence on why this is flagged (vague, overdue, upcoming, etc.)
Q1: [Most clarifying question — e.g., "What direction do you want to go on this?"]
Q2: [Secondary question if needed]

---

TASK 2: "[Task name]" (Project: [Project]) — 👤 Jeff must lead
One sentence on why this is flagged.
Q1: [Question targeting the blocker or decision]
Q2: [Question]
Q3: [Question if needed]

...
```

**For 🤖 Claude can lead tasks:** Keep questions short and directional. Jeff only needs to answer "what outcome do you want?" — Claude will handle the rest after the walk.

**Tell Jeff:** "Here's your interview script. Record yourself walking through these on Voice Memos, save the transcript to Apple Notes (`Professional Development → Voice Memo Reflections → Walk & Talk`), then come back and paste it here — or just say 'I saved the transcript' and I'll pull it."

### In-Session mode

Ask questions task by task. Present one task at a time. After Jeff answers, move to the next. Do not ask all questions at once in In-Session mode.

---

## STEP 4 — Process Transcript or Answers

### If transcript is provided (Walk & Talk mode)

1. Read the full transcript without interrupting
2. Match Jeff's answers back to the original questions and tasks — use fuzzy matching if he didn't answer in strict order
3. Extract from each answer:
   - Deliverable / definition of done
   - Blockers (explicit or implied)
   - Next physical action
   - Any new subtasks or dependencies mentioned
   - Kill decisions ("yeah we're not doing that anymore")
4. If an answer is ambiguous or incomplete, note it for follow-up — don't guess

**After processing the transcript, split tasks into two tracks:**

**Track 1 — 🤖 Claude can lead:** Immediately draft the artifact, proposal, or first output and present it to Jeff. Don't ask him to do anything — just produce it. Examples:
- Research task → produce the research summary or comparison matrix now
- "Write a proposal for X" → draft the proposal now
- "Figure out how to build Y" → generate a Claude Code prompt or working plan now
- Vague project setup → generate a project brief with subtasks and post it to Todoist

**Track 2 — 🤝 / 👤 Jeff must be involved:** Update Todoist with clarified description, next action, and subtasks per Step 5. These wait for Jeff's review before any further action.

Present Track 1 outputs first (highest leverage), then Track 2 Todoist updates.

### If in-session (typed answers)

Process answers inline as they come. For 🤖 tasks, immediately start producing the artifact after Jeff's directional answer — don't wait for a full session. For 🤝 / 👤 tasks, confirm understanding before writing back: "Got it — I'll update the task to say [X]. Correct?"

---

## STEP 5 — Write Back to Todoist

For each task with sufficient context from Jeff's answers:

**Update the task description** with:
- One-sentence definition of done: "Done when: [X]"
- Blockers (if any): "Blocked by: [X]"
- Next action: "Next: [X]"
- Any relevant context Jeff gave that isn't captured elsewhere

**Add subtasks** if Jeff described multiple steps or deliverables. Keep subtask names as concrete single actions.

**Reschedule** if Jeff indicated a new timeframe.

**Delete or complete** if Jeff said the task is no longer relevant or is already done.

**Do not modify tasks Jeff didn't discuss.** Only touch tasks that were part of this session.

Always confirm before writing back: show Jeff a one-line summary of each planned update and wait for "go" or "looks good" before calling Todoist MCP write tools.

### Todoist Organizer handoff

After writing back, produce a handoff list for the Todoist Organizer:

```
WALK & TALK → ORGANIZER HANDOFF — [DATE]

Tasks clarified and written to Todoist: [N]
Tasks still needing clarification (for next Walk & Talk): [N]

Ready for Organizer:
- All tasks updated this session now have sufficient context for prioritization and scheduling
- Run todoist-organizer next to apply P1–P4, schedule the week, detect duplicates, and label AI-ready items

Say "run the organizer" to continue.
```

The Organizer should always run after Walk & Talk in a full CoS session — Walk & Talk enriches the tasks, Organizer acts on them.

---

## STEP 6 — Session Summary

After writing back, produce a brief session summary:

```
Walk & Talk Session — [DATE]

Tasks reviewed: [N]  ([N] overdue/stalled + [N] upcoming)
Tasks clarified + updated in Todoist: [N]
Tasks killed: [N]
🤖 Claude-led actions taken this session: [N] (list each with one line on what was produced)
Follow-up needed: [N] (list them if any)

[1–2 lines on what was most useful or what still needs attention]
```

Save the summary as a markdown file:
**Path:** `/Users/jeffbeaumont/Projects/Professional Development/cos-plugin/reports/walk-talk-YYYY-MM-DD.md`

Confirm path to Jeff after saving.

---

## Voice Input Workflow

Jeff's preferred capture flow:

1. Open **Voice Memos** on iPhone
2. Record answers to the interview script on a walk (~30 min)
3. Let Apple transcribe the audio
4. Save transcript to Apple Notes: **`Professional Development → Voice Memo Reflections → Walk & Talk`**
5. Either:
   - Notify Claude in Cowork ("I saved the transcript, go grab it")
   - Or paste the transcript directly into the Cowork window

If Apple Notes MCP is connected, check the `Walk & Talk` (under `Professional Development → Voice Memo Reflections`) folder automatically when Jeff says he's back from a walk.

If the Apple Notes MCP is not connected or the folder is empty, ask Jeff to paste the transcript directly.

---

## Todoist Write Rules

These apply any time this skill writes back to Todoist:

- Never overwrite an existing description without showing Jeff the diff first
- Subtasks should be concrete single actions, not categories (✓ "Email vendor for quote" not ✗ "Vendor outreach")
- Keep descriptions under 200 words — this is a task manager, not a doc
- Don't add due dates unless Jeff explicitly gave one during the interview
- If a task is in a shared project with Jaclyn, flag any updates that affect her ownership before writing

---

## Integration Notes

- **`weekly-review` skill:** The Walk & Talk Interviewer complements the weekly review. Run it when the weekly review surfaces a cluster of unclear tasks.
- **`ca-move-pulse` skill:** If Auburn move tasks come up in the interview, route their updates through this skill's write-back step — no need to run `ca-move-pulse` separately.
- **`grill-me` skill:** For a single high-stakes task or project that needs deep stress-testing (not just clarification), use `/grill-me` instead. Walk & Talk is for breadth across many tasks; grill-me is for depth on one.

---

## Known Limitations (May 2026)

- **No last-activity timestamps from Todoist MCP:** Stall detection uses creation date as a proxy. Tasks created long ago but actively worked on may be incorrectly flagged. Jeff can dismiss these during the interview.
- **Apple Notes MCP date filtering:** Pull notes modified in the last 24 hours to find the most recent transcript. If Jeff has multiple Walk & Talk notes, ask which one to use.
- **Voice transcription accuracy:** Apple's built-in transcription is good but not perfect. If a key answer is garbled, flag it rather than guessing.
- **Todoist MCP write tools:** Confirm all writes before execution. The MCP can update task descriptions, add subtasks, and reschedule — but cannot post comments natively. If a note belongs in a comment, include it in the description instead.

---

## Session Trigger Phrases

Run this skill when Jeff says any of:
- "Walk and talk"
- "Run the interviewer"
- "Interview me about my tasks"
- "I just got back from a walk"
- "I have a transcript"
- "Help me clarify my tasks"
- "Let's go through my Todoist"
- "I recorded my answers"
- "Check Apple Notes for my walk transcript"

---

## Changelog

| Date | Change |
|------|--------|
| 2026-05-19 | v1.0 — created in Claude Cowork. First skill in the CoS daily-operations layer. |
| 2026-05-19 | v1.1 — added 10-day upcoming window; added AI Leverage Assessment (🤖/🤝/👤 labels); Track 1/2 split in Step 4 so Claude-led tasks get immediate action after walk; updated session summary format. |
| 2026-05-19 | v1.2 — clarified full Walk & Talk flow (Todoist + Apple Notes read before generating questions); added Apple Notes pull to Step 2; added Organizer handoff block at end of Step 5; corrected mode detection logic. |
