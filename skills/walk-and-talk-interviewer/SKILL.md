---
name: walk-and-talk-interviewer
description: >
  Interview Jeff about his vague, stalled, or unclear Todoist tasks to extract real context and intent — then write that context back into Todoist as improved descriptions, subtasks, and notes. Use this skill when Jeff says "walk and talk", "interview me about my tasks", "let's do a task interview", "I went for a walk", "I have a transcript", "help me clarify my tasks", "run the interviewer", or when there's a voice transcript pasted or available in the CoS — Walk & Talk Apple Notes folder. Supports two modes: Walk & Talk (Jeff answers a batch of questions on a walk and pastes/drops the transcript) and In-Session (quick inline Q&A for 1–3 tasks). Always trigger this skill when the user has a voice memo or transcript and wants to connect it to their Todoist.
---

# Walk & Talk Interviewer
### Version 1.3 — July 2026
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
| Jeff's Todoist UID | `[YOUR_TODOIST_UID]` |
| Stall threshold (days without activity) | 14 days |
| Max tasks to interview in one Walk & Talk session | 10 (to keep the walk under 30 min) |
| Workspace report path | `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/` |

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

Compile all questions into one numbered interview script. Group by task. Include the AI leverage label for each task so Jeff knows what to expect when he gets back.

**The script is written to Apple Notes and read off an iPhone on a walk. It MUST be authored as HTML — see `## Apple Notes Formatting` below. Never write plain text with ASCII dividers (`===`, `---`) or ALL-CAPS pseudo-headers into an Apple Note; Apple Notes is a rich-text surface and that output is unreadable on a phone.**

Format:

```html
<h1>Walk &amp; Talk — [TOPIC]</h1>
<p style="font-size: 18px"><b>[DATE] · [N] tasks · ~[N×3] min</b></p>
<p style="font-size: 18px">Answer out loud, in order if you can. Skip freely — just say the number.</p>

<h2>1. [Task name] — 🤖 Claude can lead</h2>
<p style="font-size: 18px"><i>Flagged because: [one sentence — vague, overdue, upcoming, stalled].</i></p>
<p style="font-size: 18px"><b>Q1.</b> [Most clarifying question]</p>
<p style="font-size: 18px"><b>Q2.</b> [Secondary question if needed]</p>

<h2>2. [Task name] — 👤 Jeff must lead</h2>
<p style="font-size: 18px"><i>Flagged because: [one sentence].</i></p>
<p style="font-size: 18px"><b>Q1.</b> [Question targeting the blocker or decision]</p>
<p style="font-size: 18px"><b>Q2.</b> [Question]</p>
```

**For 🤖 Claude can lead tasks:** Keep questions short and directional. Jeff only needs to answer "what outcome do you want?" — Claude will handle the rest after the walk.

**Recommended answers.** When the session is a decision or strategy grill rather than task clarification, follow every question with an italic `<p><i>My take:</i> …</p>` line giving Claude's recommended answer. Jeff can then agree, disagree, or push back instead of generating from scratch — far higher yield per minute of walking. (This mirrors `grill-with-docs`, which requires a recommended answer per question.)

**Write the note, then tell Jeff:** create the note directly via Apple Notes MCP in `Professional Development → Voice Memo Reflections → Walk & Talk`, then say: "Question set is in Apple Notes under [note name]. Record your answers on Voice Memos, drop the transcript in that same note, then come back and paste it here — or just say 'I saved the transcript' and I'll pull it."

**Cap note length.** If a set exceeds ~30 questions, split into two notes (`— Part 2` or `— Block [X]`) and cross-link them with a closing line. Long single notes are hard to scroll while walking.

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
**Path:** `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/walk-talk-YYYY-MM-DD.md`

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

## Apple Notes Formatting

**Rule: every note this skill writes is authored in HTML. No exceptions.**

Apple Notes is a rich-text surface. The `add_note` / `update_note_content` MCP tools accept HTML in the `content` / `new_content` field and Apple Notes renders it as native styled text. Writing plain text with ASCII dividers and ALL-CAPS headers produces a wall of undifferentiated 11px text that is genuinely hard to read on a phone mid-walk — which defeats the purpose of the skill.

### Verified tag support (tested 2026-07-28, macOS Apple Notes via apple-notes MCP)

| Tag | Result | Use for |
|-----|--------|---------|
| `<h1>` | Apple Notes **Title** — 21px bold | Note title line |
| `<h2>` | Apple Notes **Heading** — 15px bold | Section / block headers |
| `<p style="font-size: 18px">` | **15px plain — USE THIS FOR ALL BODY TEXT** | Every question and answer line |
| `<b>` | Bold | Question numbers, key terms, emphasis |
| `<i>` | Italic | "My take:" recommendations, flag reasons |
| `<u>` | Underline | Rare — avoid, reads as a link |
| `<ul><li>` | Proper bullet list | Option lists, sub-points |
| `<p>` | 11px — **too small to read on a phone** | Don't use bare `<p>` for body |

### Font sizing — the one that bit us (2026-07-28)

Apple Notes **snaps all text to three presets: 21px / 15px / 11px.** It does not honor arbitrary sizes, and it silently discards most sizing markup:

| Written | Rendered |
|---|---|
| `<p>text</p>` | 11px — Apple Notes "Body", too small |
| `<span style="font-size: 14px">` | **11px — style ignored** |
| `<div style="font-size: 16px">` | **11px — style ignored** |
| `<font size="4">` | **11px — ignored** |
| `<p style="font-size: 18px">` | **15px — works** ✓ |

Only an inline `style` on the `<p>` itself is honored, and it snaps to the nearest preset. **Default every body paragraph to `<p style="font-size: 18px">`.** Bare `<p>` renders at 11px, which Jeff has explicitly flagged as too small to read while walking. Headings stay distinguishable because `<h2>` is 15px *bold* while body is 15px plain.

### Does NOT work — known failures

- **`<ol>` silently collapses to an unordered bullet list.** The numbers are discarded with no error. **Always hardcode numbering into the text** (`<p><b>7.</b> Question…</p>`), never rely on `<ol>`.
- **`<hr>` does not render a divider.** It becomes an empty gray line. Use an `<h2>` to break sections instead.
- Do not wrap the whole note in `<html>` or `<body>` tags.
- Escape literal ampersands as `&amp;` (e.g., `Walk &amp; Talk`).

### Anti-patterns

- ✗ ASCII dividers: `===`, `---`, `***`
- ✗ ALL-CAPS lines used as headers
- ✗ Markdown syntax (`##`, `**bold**`, `- bullet`) — Apple Notes renders these as literal characters
- ✓ Real `<h1>` / `<h2>` / `<b>` / `<ul>`

### Verify after writing

After creating or updating a note, call `get_note_content` on it once and confirm the returned markup contains `<b>`, `<span style="font-size: 21px">`, or `<ul>` — not a single undifferentiated `<div>` block. If it came back flat, the content was sent as plain text; rewrite it.

---

## Todoist Write Rules

These apply any time this skill writes back to Todoist:

- Never overwrite an existing description without showing Jeff the diff first
- Subtasks should be concrete single actions, not categories (✓ "Email vendor for quote" not ✗ "Vendor outreach")
- Keep descriptions under 200 words — this is a task manager, not a doc
- Don't add due dates unless Jeff explicitly gave one during the interview
- If a task is in a shared project with [PARTNER], flag any updates that affect her ownership before writing

---

## Integration Notes

- **`weekly-review` skill:** The Walk & Talk Interviewer complements the weekly review. Run it when the weekly review surfaces a cluster of unclear tasks.
- **Auburn move tasks:** If Auburn move items come up in the interview, handle them through this skill's normal write-back step. (The standalone `ca-move-pulse` skill was retired 2026-08-19.)
- **`grill-me` skill:** For a single high-stakes task or project that needs deep stress-testing (not just clarification), use `/grill-me` instead. Walk & Talk is for breadth across many tasks; grill-me is for depth on one.

---

## Known Limitations (updated July 2026)

- **Apple Notes `<ol>` numbering is lost.** Ordered lists render as bullets. Hardcode numbers in the text. See `## Apple Notes Formatting`.
- **No delete tool in the Apple Notes MCP.** Notes created in error must be deleted by Jeff by hand — so don't create scratch/test notes in the `Walk & Talk` folder without telling him.
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
| 2026-07-28 | v1.3 — **Apple Notes formatting fixed.** Root cause: Step 3's batch format specified a plain-text code block, so walk scripts were written as ASCII-divider walls of 11px text, unreadable on a phone. Added a full `## Apple Notes Formatting` section with verified tag support (`<h1>`/`<h2>`/`<b>`/`<i>`/`<ul>` work; `<ol>` silently collapses to bullets; `<hr>` renders as a blank line), anti-patterns, and a `get_note_content` verify step. Font sizing: Apple Notes snaps to 21/15/11px presets and ignores `<span>`/`<div>`/`<font>` sizing entirely — only `<p style="font-size: 18px">` produces readable 15px body text; bare `<p>` renders at 11px, which Jeff flagged as unreadable on a phone. Rewrote the Step 3 batch format as HTML. Added the "recommended answer" (`My take:`) convention for decision/strategy grills, borrowed from `grill-with-docs`. Added a 30-question note-split cap. Added two Known Limitations (no `<ol>` numbering, no Apple Notes delete tool). |
