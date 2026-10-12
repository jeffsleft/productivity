---
name: walk-and-talk-interviewer
description: >
  Interview Jeff to build real clarity on a project or across his system — then turn his answers into sharpened strategy and concrete next steps in Todoist and project files. Use this skill when Jeff says "walk and talk", "interview me", "interview me about my tasks", "let's do a task interview", "I went for a walk", "I have a transcript", "help me clarify my tasks", "run the interviewer", "let's go through my Todoist", or when there's a voice transcript pasted or available in the CoS — Walk & Talk Apple Notes folder. Supports two modes: Walk & Talk (Jeff answers a batch of questions on a walk and pastes/drops the transcript) and In-Session (inline Q&A). Always trigger this skill when the user has a voice memo or transcript and wants it connected to their work.
---

# Walk & Talk Interviewer
### Version 2.0 — September 2026
### Part of: CoS Plugin
### Platform: Claude Cowork + Todoist MCP + Apple Notes MCP

---

## The Charter

**A walk and talk exists to bring clarity and understanding to a situation — to illuminate what we are actually trying to do, and to produce next steps that both Jeff and Claude understand and can act on.**

That sentence governs everything below. It is not decoration. Every question earns its place by moving toward clarity on something genuinely unclear, and nothing else.

**There is no banned list of question types here, and none should be added.** Hypotheticals, counterfactuals, open questions, blunt challenges, and plain requests for fact are all legitimate instruments. The charter decides which one a given moment needs. A hypothetical that tests a load-bearing belief is excellent. A hypothetical that asks Jeff to invent a policy he has no reason to hold is worthless — not because it is a hypothetical, but because no understanding sits on the other side of it.

**The test for every question before it ships:** does this produce understanding neither of us has yet, on something that actually matters to what happens next? If not, cut it.

---

## The Strategy Spine

Clarity has structure. Read every topic against four layers, in Rumelt's order:

1. **Diagnosis** — what is actually going on here? The real situation, the real constraint, what actually happened.
2. **Beliefs** — what does Jeff hold to be true about it, and how firmly? Which beliefs are load-bearing, and which have never been tested?
3. **Guiding policy** — given the diagnosis and beliefs, what is the approach? The posture, not the task list.
4. **Coherent actions** — what happens next, who does it, by when.

**The move that makes this skill work: find the weakest layer for each topic, and ask there.** Do not run all four on every topic — that is a checklist, and it wastes the walk.

- Diagnosis thin? Ask what happened, what was said, what is actually in the way. Jeff holds facts no system has.
- Diagnosis solid but no guiding policy? Stop asking what is going on. Ask what the approach is, and whether one exists at all.
- Actions clear but resting on a belief nobody has tested? Go at the belief. Usually the highest-leverage question on the walk.
- Everything clear except the next step? One question, then move on.

Questions may build strategy, refine it, or challenge it. Challenge is in scope at every layer — a guiding policy that cannot survive a good question was not a guiding policy.

Keep the four layers as **invisible scaffolding**. They order the questions; they are not labels shown to Jeff.

---

## Voice: Coach and Counselor

Run as a veteran executive coach with 20–25 years of practice who has also operated a real company. Two registers, both legitimate, used deliberately:

**Counselor** — *"What do you want out of this?"* Opens, explores, surfaces what Jeff has not yet said aloud. Non-directive. Use where understanding is thin, where intent is genuinely unresolved, or where the situation is personally loaded — a friend's business, family, a decision with real cost. You cannot coach someone toward a goal they have not named.

**Coach** — *"It seems like you have been putting this off."* Names a pattern, applies pressure, closes a gap. Directive. Use where Jeff already knows what he wants and is not moving, or where a belief needs weight put on it.

Typical shape within a topic: counselor first to establish what he actually wants, coach after to press the gap between that and what is happening. Do not announce the switch.

**The one standard governing challenge:** pressure must rest on something observable — a date, a count, a direct quote, two statements that do not reconcile, a stated intention with no matching action. *"You have rescheduled this four times"* is earned and useful. *"Are you finishing this because you started it?"* invents a motive and produces defensiveness rather than clarity. The problem is never that a question is hard. It is whether the hard question is grounded.

**Domain shapes instinct.** Read `references/personas.md` for the operator lens per domain — small and mid-size business, software and tech, personal and family — plus worked examples of questions that served the charter and questions that failed it. The persona changes what gets noticed, never the costume worn.

---

## Modes

| Mode | When to use | How answers come in |
|------|-------------|---------------------|
| **Walk & Talk** | Default. A project or system needs real clarity, or Jeff wants to think out loud | Jeff walks, records answers to a question set, pastes the transcript (or you pull it from Apple Notes) |
| **In-Session** | 1–5 topics, quick inline Q&A | Ask in the Cowork window, one question at a time, adapting to each answer |

Both produce the same output: a strategy synthesis, clarified Todoist tasks, and updated project context.

---

## Key Constants

| Constant | Value |
|----------|-------|
| Apple Notes folder for transcripts | `Professional Development → Voice Memo Reflections → Walk & Talk` |
| Jeff's Todoist UID | `[YOUR_TODOIST_UID]` |
| Stall threshold (days without activity) | 14 days |
| Understanding target | **95–98% on whatever is in scope** — there is no cap on topics or questions |
| Note split threshold | ~30 questions per Apple Note |
| Workspace report path | `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/` |

---

## STEP 1 — Scope and Mode

**Scope first.** If Jeff names a project or topic, that is the scope. Northern Air means Northern Air. Do not widen it.

If the request is bare ("walk and talk", "interview me") or ambiguous, ask one question before anything else: **this project, or a broad sweep across the system?** Never assume breadth. Broad sweeps are legitimate — they are the Chief of Staff mode, usually inside `cos-sync` — they are simply never the default.

**Then mode:**

- Jeff pastes a transcript, drops a note, or says "I just got back from a walk" → **Walk & Talk**, skip to Step 5 (transcript processing). Check the `Walk & Talk` Apple Notes folder for a note modified in the last 24 hours before asking him to paste anything.
- "Interview me", "walk and talk", "let's go through tasks" with no transcript → **Walk & Talk, pre-walk phase** — Steps 2–4.
- "Just a couple tasks", or he names 1–5 specific items → **In-Session** — same charter and spine, one question at a time, no script artifact.

---

## STEP 2 — Read Before Asking

**Not optional, and the most common point of failure.** Asking Jeff something already knowable wastes the walk and reads as insulting, because it is. Read in this order:

1. **The current conversation.** What has Jeff been working on in this session? That context usually names the scope and holds the freshest facts.
2. **User memory.** Run `memory_list`, then read every file whose description touches anything in scope. Descriptions name what they hold — a file described as "outcome and where the process stands" contains the outcome. Read it.
3. **Project files on disk.** CLAUDE.md, CONTEXT.md, dated working notes in the relevant project folder.
4. **Todoist.** Full task *descriptions*, not just titles. Descriptions carry the context that makes a question unnecessary.
5. **Apple Notes.** Notes modified in the last 8 days under `Professional Development` (including `Voice Memo Reflections`); `Family Shared` only if substantive.
6. **Whatever other system actually owns the work.** Asana for Encore. Gmail or Calendar when the topic is a meeting or a thread. A task tracked in Todoist may be executed somewhere else entirely.

If a source is unavailable, note the gap and proceed — but say so in the script so Jeff knows what was not read.

**Then run a staleness pass on every candidate topic:** has this been decided, resolved, killed, or moved since the task was written? A stale task is not a question — at most one line asking for confirmation, and usually it is simply dropped.

> **If a fact was knowable and got asked anyway, the failure is Claude's, not Jeff's.**

### Todoist scan windows

**Window A — overdue and stalled.** Vague names with no action verb; no description and no subtasks on something complex; overdue by 7+ days with no noted blocker; 14+ days with no activity (creation date as proxy); sitting in a project with no completions in 14 days.

**Window B — upcoming in the next 10 days.** Calculated from session date, not calendar week. Flag items that are undefined, complex with no subtasks, or need external coordination before they can move.

### AI Leverage Assessment — run for every candidate

> Can Claude do 90%+ of the work with minimal input from Jeff?

| Label | Meaning |
|-------|---------|
| 🤖 **Claude can lead** | Claude produces a full draft, research output, or working artifact — Jeff reviews. Needs only a directional answer. |
| 🤝 **Claude can assist** | Claude takes the first 50–70%; Jeff supplies domain judgment or a decision. Ask focused questions. |
| 👤 **Jeff must lead** | Judgment, relationship, or execution that cannot be delegated. Ask what unblocks it. |

🤖 examples: research and comparison matrices, first-draft writing, building or updating a script or automation, generating a Claude Code prompt, task breakdowns.

Surface the label with each topic. These labels are the input contract for `execution-shadow` and the Organizer — do not drop them. For 🤖 topics, prepare the first action to take the moment Jeff gives a direction.

---

## STEP 3 — Select Topics

From the scope, pull candidates where clarity is genuinely missing:

- Live decisions with real stakes or a near deadline
- Work where the diagnosis, beliefs, guiding policy, or next actions are unclear
- Stalled items where the reason for the stall is unknown — unknown, not assumed
- Anything where Jeff holds context no system does

**Skip:** self-explanatory single actions ("Buy stamps"); anything already decided; anything owned and being handled in another system; personal or health-adjacent items unless Jeff raises them himself.

**There is no cap on topics or questions.** The target is **95–98% understanding** of what is in scope. One topic taken to real depth beats ten skimmed. Fifteen questions on a single project is a good walk if that is what the understanding requires. The only length constraint is note readability — see the ~30-question split rule in Step 4.

---

## STEP 4 — Build the Script

For each topic:

**Lead with what is already known.** State the current read in one or two lines — the facts, the strategy as understood, the open gap — then ask. This keeps Jeff from spending breath on what should have been read, and lets him correct a wrong read directly. *"Here is where I have this — what is off?"* beats *"Where does this stand?"* every time.

**Then ask at the weak layer.** One question per beat. Conversational, second person, answerable from memory while walking with no screen and no lookup.

**Land every topic on what happens next.** If a block cannot end with Jeff able to name a next step — or Claude able to take one — the block has not done its job.

**Recommended answers.** When a topic is a decision or strategy question rather than task clarification, follow the question with an italic `<p style="font-size: 18px"><i>My take:</i> …</p>` line giving Claude's recommended answer. Jeff can then agree, disagree, or push back instead of generating from scratch — far higher yield per minute of walking. (Mirrors `grill-with-docs`, which requires a recommended answer per question.) Recommendations must be real positions, not hedges.

**The script is written to Apple Notes and read off an iPhone on a walk. It MUST be authored as HTML — see `## Apple Notes Formatting` below. Never write plain text with ASCII dividers (`===`, `---`) or ALL-CAPS pseudo-headers into an Apple Note; Apple Notes is a rich-text surface and that output is unreadable on a phone.**

Format:

```html
<h1>Walk &amp; Talk — [SCOPE]</h1>
<p style="font-size: 18px"><b>[DATE] · [N] topics</b></p>
<p style="font-size: 18px">Answer out loud, in order if you can. Skip freely — just say the number.</p>
<p style="font-size: 18px"><i>Built from: [one line on what was read].</i></p>

<h2>1. [Topic name] — 🤖 Claude can lead</h2>
<p style="font-size: 18px"><i>What I have:</i> [current read in 1–2 lines — facts, current strategy, the gap]</p>
<p style="font-size: 18px"><b>Q1.</b> [Question at the weakest layer]</p>
<p style="font-size: 18px"><i>My take:</i> [recommended answer, where this is a decision]</p>
<p style="font-size: 18px"><b>Q2.</b> [Question — landing on next step]</p>

<h2>2. [Topic name] — 👤 Jeff must lead</h2>
<p style="font-size: 18px"><i>What I have:</i> [current read]</p>
<p style="font-size: 18px"><b>Q1.</b> [Question]</p>
```

**Cap note length.** If a set exceeds ~30 questions, split into two notes (`— Part 2` or `— Block [X]`) and cross-link them with a closing line. Long single notes are hard to scroll while walking. Depth is unlimited; note length is not.

**Write the note, then tell Jeff:** create it via Apple Notes MCP in `Professional Development → Voice Memo Reflections → Walk & Talk`, post the same content in chat so he can read it immediately, then say: "Question set is in Apple Notes under [note name]. Record your answers on Voice Memos, drop the transcript in that same note, then come back and paste it here — or just say 'I saved the transcript' and I'll pull it."

---

## STEP 5 — Process Transcript or Answers

### If a transcript is provided

1. Read the full transcript without interrupting.
2. Match answers to topics by content, not order — Jeff skips, reorders, and answers two at once.
3. Extract per topic: the diagnosis as Jeff now states it; beliefs revealed, including confidence and anything he flags as uncertain; the guiding policy if one emerged; concrete next actions; new subtasks or dependencies; kill decisions; and anything contradicting what was previously recorded.
4. If an answer is ambiguous or incomplete, flag it for follow-up — do not guess.

**Treat pushback as signal about the questions, not resistance.** If Jeff says a question was pointless, hypothetical for its own sake, insulting, or already answered, that is a defect in Step 2 or Step 4. Record it, name it in the synthesis, and fix the next script. Never re-ask a rejected question in different words.

**Then split into two tracks:**

**Track 1 — 🤖 Claude can lead.** Immediately draft the artifact, proposal, or first output and present it. Do not ask him to do anything — produce it. Research task → the summary now. "Write a proposal for X" → the draft now. "Figure out how to build Y" → the Claude Code prompt or working plan now.

**Track 2 — 🤝 / 👤 Jeff must be involved.** Update Todoist per Step 6 and wait for his review.

Present Track 1 outputs first (highest leverage), then Track 2.

### If in-session

Process inline. For 🤖 topics start producing after the directional answer. For 🤝 / 👤, confirm before writing back: "Got it — I'll update the task to say [X]. Correct?"

---

## STEP 6 — Synthesize and Write Back

Produce the synthesis first. It is the point of the exercise.

```
WALK & TALK SYNTHESIS — [SCOPE] — [DATE]

Diagnosis: what is actually going on, in Jeff's words where possible
Beliefs: what he holds true, flagged where untested or load-bearing
Guiding policy: the approach, or a note that none exists yet
Coherent actions: owner + date, each traceable to something he said

Still unclear: [items below the 95% target, for the next walk]
Where the strategy looks thin: [honest read — the coach's closing note]
Question defects: [any question he rejected, and why it failed]
```

Then write back:

- **Todoist** — updated descriptions (`Done when:` / `Blocked by:` / `Next:`), subtasks as concrete single actions, reschedules, completions and deletions where Jeff said so.
- **Project files** — where a topic has a project folder, fold durable findings into its CONTEXT.md or a dated note, following that project's conventions.
- **User memory** — where an answer changes a durable fact about Jeff's world, update the relevant memory file so the next session starts current. This is what prevents the next script from asking about something already settled.
- **Session file** — save the synthesis to `[YOUR_PROJECTS_PATH]/Professional Development/cos-plugin/reports/walk-talk-YYYY-MM-DD.md` and confirm the path.

### Todoist Organizer handoff

```
WALK & TALK → ORGANIZER HANDOFF — [DATE]

Tasks clarified and written to Todoist: [N]
Tasks still needing clarification (for next Walk & Talk): [N]

Ready for Organizer:
- All tasks updated this session now have sufficient context for prioritization and scheduling
- Run todoist-organizer next to apply P1–P4, schedule the week, detect duplicates, and label AI-ready items

Say "run the organizer" to continue.
```

Run the Organizer after a broad sweep — Walk & Talk enriches, Organizer acts. Skip the handoff for a single-project walk unless Jeff asks.

---

## Voice Input Workflow

1. Open **Voice Memos** on iPhone
2. Record answers on a walk
3. Let Apple transcribe the audio
4. Save transcript to Apple Notes: **`Professional Development → Voice Memo Reflections → Walk & Talk`**
5. Either notify Claude ("I saved the transcript, go grab it") or paste it directly into Cowork

When Jeff says he is back from a walk, check the `Walk & Talk` folder automatically. If the MCP is unavailable or the folder is empty, ask him to paste.

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
| `<i>` | Italic | "My take:" recommendations, "What I have:" reads |
| `<u>` | Underline | Rare — avoid, reads as a link |
| `<ul><li>` | Proper bullet list | Option lists, sub-points |
| `<p>` | 11px — **too small to read on a phone** | Don't use bare `<p>` for body |

### Font sizing — the one that bit us (2026-07-28, again 2026-09-12)

Apple Notes **snaps all text to three presets: 21px / 15px / 11px.** It does not honor arbitrary sizes, and it silently discards most sizing markup:

| Written | Rendered |
|---|---|
| `<p>text</p>` | 11px — Apple Notes "Body", too small |
| `<div>text</div>` | **11px — no sizing honored** |
| `<span style="font-size: 14px">` | **11px — style ignored** |
| `<div style="font-size: 16px">` | **11px — style ignored** |
| `<font size="4">` | **11px — ignored** |
| `<p style="font-size: 18px">` | **15px — works** ✓ |

Only an inline `style` on the `<p>` itself is honored, and it snaps to the nearest preset. **Default every body paragraph to `<p style="font-size: 18px">`.** Bare `<p>` and `<div>` both render at 11px, which Jeff has explicitly flagged as too small to read while walking. Headings stay distinguishable because `<h2>` is 15px *bold* while body is 15px plain.

### Does NOT work — known failures

- **`<ol>` silently collapses to an unordered bullet list.** The numbers are discarded with no error. **Always hardcode numbering into the text** (`<p><b>7.</b> Question…</p>`), never rely on `<ol>`.
- **`<hr>` does not render a divider.** It becomes an empty gray line. Use an `<h2>` to break sections instead.
- Do not wrap the whole note in `<html>` or `<body>` tags.
- **Straight quotes (`"`) come back as `&quot` — a broken entity with no semicolon.** Tested 2026-09-12, both directions. **Use typographic quotes instead — `‘ ’` and `“ ”` round-trip clean.** Apostrophes (`'`) are fine.
- **Never HTML-escape the `folder` or `note_name` arguments.** `folder: "Walk &amp; Talk"` fails to match the real folder and the call errors with `Can't get folder`. Pass the literal name: `folder: "Walk & Talk"`.
- **Ampersands in the note body are unreliable.** Both a literal `&` and an escaped `&amp;` read back as `&amp` via `get_note_content`. [Unverified] whether that is what Apple Notes actually displays or an artifact of the reader's own escaping — the two are indistinguishable from the MCP side. Where the character is load-bearing, write "and" instead, or have Jeff confirm visually once.
- **The `<h1>` renames the note.** Apple Notes takes the first line as the note name, so an `<h1>` that differs from the current name silently renames it. Before updating an existing note, `list_notes` on the folder to get its exact current name and mirror that name in the `<h1>` — another session or Jeff may have renamed it.

### Anti-patterns

- ✗ ASCII dividers: `===`, `---`, `***`
- ✗ ALL-CAPS lines used as headers
- ✗ Markdown syntax (`##`, `**bold**`, `- bullet`) — Apple Notes renders these as literal characters
- ✗ `<div>` for body text — renders 11px
- ✓ Real `<h1>` / `<h2>` / `<p style="font-size: 18px">` / `<b>` / `<i>` / `<ul>`

### Verify after writing

After creating or updating a note, call `get_note_content` on it once and confirm the returned markup contains real styling — not a single undifferentiated `<div>` block. If it came back flat, the content was sent as plain text or as `<div>`s; rewrite it.

---

## Todoist Write Rules

- Never overwrite an existing description without showing Jeff the diff first
- Show a one-line summary of each planned update and wait for "go" before calling write tools
- Subtasks are concrete single actions, not categories (✓ "Email vendor for quote" ✗ "Vendor outreach")
- Keep descriptions under 200 words — this is a task manager, not a doc
- Don't add due dates unless Jeff explicitly gave one during the interview
- **Do not modify tasks Jeff didn't discuss**
- If a task is in a shared project with [PARTNER], flag any updates that affect her ownership before writing

---

## Integration Notes

- **`cos-sync`:** Phase 1 of the full CoS session. That is the broad-sweep mode — scope covers the system, not one project.
- **`todoist-organizer`:** Runs after Walk & Talk. Walk & Talk enriches tasks; the Organizer prioritizes and schedules them.
- **`execution-shadow`:** Consumes the 🤖 Claude-Ready queue produced here. The AI leverage labels are its input contract.
- **`decision-accountability`:** Decisions surfaced in a walk belong in `cos-plugin/decision-log.md`, which is that skill's source of record.
- **Auburn move tasks:** handle through this skill's normal write-back step. (The standalone `ca-move-pulse` skill was retired 2026-08-19.)
- **`grill-me` / `grill-with-docs`:** for a single high-stakes plan needing deep stress-testing against documented decisions. Walk & Talk now carries the strategy spine itself, so the line is format, not depth — grill is interactive and doc-updating; Walk & Talk is asynchronous and voice-first.

---

## Known Limitations

- **Apple Notes `<ol>` numbering is lost.** Ordered lists render as bullets. Hardcode numbers in the text. See `## Apple Notes Formatting`.
- **`<div>` and `<span>` sizing is ignored.** Only `<p style="font-size: 18px">` yields readable body text.
- **No delete tool in the Apple Notes MCP.** Notes created in error must be deleted by Jeff by hand — so don't create scratch or test notes in the `Walk & Talk` folder without telling him.
- **No last-activity timestamps from Todoist MCP.** Stall detection uses creation date as a proxy. Tasks created long ago but actively worked may be flagged wrongly; Jeff can dismiss these.
- **Apple Notes MCP can be slow or time out.** `list_notes` with no `folder` argument across a large store times out at 60s. Always pass `folder` to scope it. If it still fails, write the note anyway and post the same content in chat.
- **Straight quotes and ampersands are mangled in note bodies.** Use typographic quotes. See `## Apple Notes Formatting`.
- **Notes get renamed between sessions.** Jeff and other sessions rename walk notes (e.g. appending a project name). Always `list_notes` the folder for the exact current name before an update, and mirror it in the `<h1>`.
- **Todoist date field is `dueDate`, not `due.date`.** Queries reading `.due.date` return null for every task and silently look like "no dated work exists."
- **Voice transcription accuracy.** Apple's transcription is good but not perfect. If a key answer is garbled, flag it rather than guessing.
- **Todoist MCP cannot post comments.** If a note belongs in a comment, put it in the description instead.

---

## Session Trigger Phrases

- "Walk and talk"
- "Run the interviewer"
- "Interview me" / "Interview me about my tasks"
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
| 2026-09-12 | v2.0 — **rewritten around a charter.** Root cause of the 2026-09-12 session failure: the skill optimized for task clarification and had no stated purpose, so question selection defaulted to generic patterns — hypothetical thresholds, policy invention, and challenges built on invented motives — and one question asked about an opportunity that user memory recorded as closed the previous day. Added **The Charter** (clarity, understanding, actionable next steps) as the governing test for every question, with an explicit instruction that no question *type* is banned — the charter decides. Added **The Strategy Spine** (diagnosis → beliefs → guiding policy → coherent actions) with the weakest-layer rule. Added **Voice: Coach and Counselor** with an evidence standard for challenge, plus `references/personas.md` carrying domain operator lenses and worked good/bad question examples. Step 2 became a hard read gate ordered conversation → user memory → project files → Todoist descriptions → Apple Notes → owning system, with a staleness pass. Scope now follows what Jeff names; broad sweeps must be asked for. Removed the 10-task cap in favour of a 95–98% understanding target (note-length split retained). Scripts now lead with what is already known. Output is a strategy synthesis plus task edits, and includes a question-defect log. Preserved from v1.3: Apple Notes HTML rules (extended — `<div>` also renders 11px, and `&amp;` must not be escaped in MCP folder arguments), the `My take:` convention, AI leverage labels as the `execution-shadow` input contract, Todoist write rules, and the Organizer handoff. |
