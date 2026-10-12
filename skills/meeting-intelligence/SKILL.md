---
name: meeting-intelligence
description: >
  Full meeting lifecycle management — prep, rehearsal (if needed), note capture, and post-meeting synthesis. Use when Jeff says "prep me for [meeting]", "meeting prep", "I have a meeting with [person]", "let's debrief [meeting]", "I need to prep for an interview", "write up my meeting notes", or any time a calendar event or Granola transcript needs to be turned into structured prep or follow-up. Covers all meeting types: Finance Team, 1:1s, GLM calls, ad hoc, and job search interviews.
version: 2.2
---

# Meeting Intelligence
### Version 2.2 — June 2026
### Part of: Meeting Intelligence Plugin (standalone, event-triggered)
### Platform: Claude Cowork + Google Calendar MCP + Google Drive/Docs MCP + Apple Notes MCP + Todoist MCP

---

## What This Skill Does

Manages the full meeting lifecycle in four phases:

1. **Prep** — brief before the meeting (agenda, context, questions, watch-outs)
2. **Rehearsal** — optional Q&A simulation for interviews or high-stakes presentations
3. **Notes** — structured capture during or after the meeting
4. **Synthesis** — post-meeting summary, **blunt self-evaluation**, action items → Todoist, record written to Google Docs

Each phase is standalone, BUT note capture and synthesis are wired together: **finishing NOTE CAPTURE automatically continues into SYNTHESIS — including the self-evaluation — in the same run.** Synthesis is the tail of capture, not a separate thing Jeff has to remember to trigger. (This closes the project's #1 failure mode: the back half silently never running.)

---

## Meeting Typology

Every meeting is classified using the Breunsbach framework:

| Type | Purpose | Failure mode |
|------|---------|-------------|
| **Informational** | Transfer knowledge or context | No clear owner; passive listening with no output |
| **Alignment** | Reach shared understanding | Assumed alignment that wasn't verified |
| **Decision-making** | Make a choice with authority | Right people not in room; decision not recorded |

Classify the meeting at Phase 1 open. The classification shapes the agenda, questions, and synthesis.

---

## Storage — Where Things Live

**System of record = Google Docs** (decided 2026-06-07). Meeting notes are saved as Google Docs in Drive.

| What | Where | Why here |
|------|-------|----------|
| Per-meeting notes (synthesis + self-eval) | **Google Docs**, `Meeting Notes` Drive folder | The record. One Doc per call. |
| Growth Log (running, cross-call) | **Apple Notes** — `Meeting Intelligence → Growth Log` | Append-style running log; Drive MCP can't append (see constraint below). |
| Job-Search Pipeline (one line per interview) | **Apple Notes** — `Job Search → Candidate Pipeline` | Same append constraint. |
| Operating manual / current state | **Apple Notes** — `Meeting Intelligence — Operating Manual` | Glanceable on phone/iPad. |

### Google Docs structure (the record)

The connected Google Drive MCP is **create-and-read only — it CANNOT modify an existing Doc.** So the model is **one Doc per call** (no appending to a running doc):

| Meeting type | Doc name |
|-------------|---------|
| Recurring (1:1, team, monthly) | `[YYYY-MM-DD] [Meeting Name]` — one dated Doc per occurrence |
| One-off / ad hoc | `[YYYY-MM-DD] [Meeting Name]` |
| Interview | `[YYYY-MM-DD] Interview — [Company] — [Role]` |

To see history for a recurring meeting, `search_files` for the meeting name and read the most recent dated Docs. Do NOT try to append to a prior Doc — create a fresh dated one each time.

**Default:** Google Docs. At output time, offer alternatives:
> "I'll save this as a Google Doc by default. Say 'Apple Note', 'Word doc', or 'paste only' if you want it elsewhere."

**Constraint to remember:** because Drive is create-only here, the Growth Log and Pipeline stay in Apple Notes (which supports in-place update). Revisit if an editable Google Docs connector (batchUpdate) is added.

---

## PHASE 1 — PRE-MEETING PREP

**Trigger:** "Prep me for [meeting]" / "I have a meeting with [person] at [time]" / "prep" + any meeting reference

### Step 1 — Gather context

Ask for what you don't already know. Keep it to one block:

```
Meeting Intelligence — Prep

A few quick questions:

1. Meeting: What's it called, and who's attending?
   (Skip if I can pull it from Google Calendar)

2. Type: Is this a recurring meeting or one-off?

3. Goal: What does a good outcome look like for you?

4. Anything sensitive, risky, or unusual going into this one?
   (open issues, relationship dynamics, decisions pending)

5. Any prior notes I should pull?
   (Say "pull [note name]" and I'll read the Apple Note)
```

If Google Calendar MCP is available, pull the event first — pre-populate what you can and only ask for what's missing.

### Step 2 — Pull context

Pull in order of availability:
- **Google Calendar** — event details, attendees, time, duration
- **Google Docs** — prior meeting Docs for this person/meeting (`search_files` by meeting name, read the most recent)
- **Todoist** — any open tasks related to this person or project
- **Decision Log** — any logged decisions involving this person or topic (read `CoS — Decision Log` Apple Note)

### Step 3 — Generate the brief

Produce a scannable brief designed to be reviewed in under 5 minutes:

---

**MEETING BRIEF — [Meeting Name] — [Date]**

**Type:** [Informational / Alignment / Decision-making]
**Attendees:** [Names, roles]
**Duration:** [X min]
**Goal:** [One sentence — what a good outcome looks like]

---

**Context**
2–3 sentences on where things stand. Pull from prior notes, open Todoist tasks, and any relevant decisions. Flag anything that has changed since last meeting.

---

**Open items from last meeting** (if recurring)
- [Item] — [status: done / still open / needs discussion]

---

**Recommended agenda**
| Time | Topic | Who leads |
|------|-------|-----------|
| 0:00 | [Opening / framing] | Jeff |
| 0:05 | [Topic 1] | [Person] |
| ... | ... | ... |
| -5:00 | [Next steps + close] | Jeff |

Adjust time allocations to meeting length. Decision-making meetings need a clear decision moment built in.

---

**Questions to ask**
3–4 open-ended, high-value questions. Specific to this meeting and these attendees. Not generic.

---

**Watch-outs**
- Sensitive topics to handle carefully
- Questions they may ask (and how to handle)
- Relationship or dynamic notes

---

After delivering the brief, offer:
> "Want me to run a rehearsal before this meeting? I can simulate the other side of the conversation — useful for interviews, difficult 1:1s, or any meeting where you want to stress-test your talking points."

---

## PHASE 2 — REHEARSAL (OPTIONAL)

**Trigger:** Jeff says "yes" to the rehearsal offer, or invokes directly: "rehearse [meeting]" / "simulate [interview]" / "grill me on this"

**When to use:** Interviews, high-stakes presentations, difficult conversations (e.g., performance feedback, budget asks, escalations).

### Interview rehearsal mode

For job search interviews specifically:

1. Ask Jeff for the role, company, and interview type (behavioral / technical / case / executive)
2. Pull any context from the Recruiting Engine if available
3. Run a structured mock session:
   - Open with: "I'll play the interviewer. Answer naturally — I'll push back, probe, and ask follow-ups. Tell me when you want to stop for feedback."
   - Ask questions appropriate to the interview type
   - After each answer: brief coaching note (what landed, what to sharpen)
   - End with: overall prep assessment + 2–3 things to tighten before the real thing

### General rehearsal mode

For non-interview meetings:
- Simulate the other person's perspective based on context provided
- Push back on weak arguments
- Surface the hard question they're likely to ask
- Help Jeff find his clearest answer

---

## PHASE 3 — NOTE CAPTURE

**Trigger:** "I'm in the meeting" / "taking notes" / "here's what happened" / "capture this"

Two modes:

### Live capture (Jeff types during meeting)
Accept bullet-point input as Jeff types. Do not restructure mid-meeting — just receive and confirm. After meeting ends, move to Phase 4.

### Post-meeting input
Accept one of:
- **Granola transcript paste** — Jeff pastes the raw transcript
- **Voice memo / rough notes** — Jeff pastes or describes what happened
- **Manual summary** — Jeff writes what he remembers

For Granola transcripts: acknowledge receipt, confirm meeting name and date, then proceed to Phase 4.

---

## PHASE 4 — POST-MEETING SYNTHESIS

**Trigger:** Notes or transcript received, or "debrief [meeting]" / "write up [meeting]"

### Step 1 — Produce the synthesis

```
MEETING NOTES — [Meeting Name] — [YYYY-MM-DD]

TYPE: [Informational / Alignment / Decision-making]
ATTENDEES: [Names]
DURATION: [X min]

SUMMARY
2–3 sentence narrative of what happened. What was the meeting actually about?
What shifted, got decided, or became clearer?

KEY DECISIONS
- [Decision] — [owner if applicable]
(Leave blank if no decisions were made — do not force it)

ACTION ITEMS
- [ ] [Action] — [owner] — due [date or "TBD"]
(Only include genuine next steps with an owner. Do not list vague intentions.)

OPEN QUESTIONS
- [Question that wasn't resolved and needs follow-up]
(Only include if real — not filler)

NOTES FOR NEXT MEETING
[1–2 things worth remembering for the next conversation with this person]
```

### Step 2 — Self-Evaluation (runs on EVERY call)

This is not optional and not flattering. Evaluate how **Jeff** showed up, using the transcript and any Granola notes as evidence. Default register: **blunt — name what was weak, no softening, no praise sandwiches.** Jeff has explicitly asked to be coached hard here. Ground every judgment in a specific quote or moment from the transcript; never evaluate from impression alone. If the meeting was unrecorded (e.g. in-person Finance Team), say so and run the lighter version from Jeff's own recollection.

**Which version to run:**
- **Interview** (recruiter screen, hiring manager, panel, networking-with-intent) → run the **full scored rubric** below.
- **Every other call** (1:1, team, GLM, ad hoc) → run the **3-line version** below.

#### Full interview rubric

Score each dimension **1–5** (1 = hurt you, 3 = competent, 5 = strong differentiator). Each score REQUIRES a supporting quote or moment from the transcript.

```
SELF-EVALUATION — [Interview — Company — Role] — [YYYY-MM-DD]

| Dimension | Score | Evidence (quote / moment) |
|-----------|-------|---------------------------|
| Narrative clarity — was the career story tight and led with the point? | _/5 | "..." |
| Evidence & specificity — concrete metrics/proof vs. vague claims? | _/5 | "..." |
| Question handling — answered what was asked, structured (STAR where apt), no rambling? | _/5 | "..." |
| Executive presence — calm, concise, conviction without overclaiming? | _/5 | "..." |
| Fit & differentiation — did he show why HIM specifically for THIS role? | _/5 | "..." |
| Curiosity / questions asked — did his questions land as senior and informed? | _/5 | "..." |

WEIGHTED READ: [1–2 sentences. The honest verdict — would this advance him? Where did he lose the room?]

TWO THINGS TO SHARPEN BEFORE THE NEXT ONE:
1. [Specific, actionable — tied to a moment above]
2. [Specific, actionable]

ONE THING THAT WORKED — KEEP DOING IT:
- [Only if genuinely earned. If nothing did, say so plainly.]
```

#### 3-line version (non-interview calls)

```
SELF-EVALUATION — [Meeting] — [YYYY-MM-DD]
- Strongest move: [what Jeff did well, with the moment — or "nothing notable"]
- Weakest move: [what undercut him — vague point, talked over someone, ducked a decision, over-explained]
- Sharpen next time: [one concrete behavioral fix]
```

The self-evaluation is written **into the same Google Doc** as the synthesis (a `SELF-EVALUATION` section below the notes). For interviews, also offer to log the two sharpen-points to the Growth Log Apple Note:
> "Want the two sharpen-points added to your Growth Log so the pattern shows up over time?"

### Step 3 — Action item handling

For each action item owned by Jeff:
> "I have [N] action items for you. Want me to create Todoist tasks for any of these?"

List them. Jeff says "all", "none", or names specific ones. Create tasks with:
- Content = action description
- Project = relevant project (ask if unclear)
- Due date = if mentioned in meeting; otherwise leave blank
- Description = meeting name + date for context

### Step 4 — Save to Google Docs

Save the synthesis **and** the self-evaluation as a **single new Google Doc** in the `Meeting Notes` Drive folder:
- Create the folder if it doesn't exist (`search_files` first; `create_file` if missing).
- Doc name follows the convention: `[YYYY-MM-DD] [Meeting Name]` (or `[YYYY-MM-DD] Interview — [Company] — [Role]`).
- One Doc per call — never try to append to a prior Doc (Drive MCP is create-only).

Confirm before writing:
> "I'll save this as a new Google Doc: '[doc name]' in your Meeting Notes folder. Good to go?"

### Step 5 — Recap distribution (offer)

After saving, offer to send the recap to attendees:
> "Want a recap to send the attendees? I can draft it as an email or a Slack message."

If yes:
- Pull attendees from the synthesis / calendar event.
- Draft a SHORT recap (not the full notes): 2–3 line summary, the decisions, and each attendee's action items with owners + dates.
- Tone = Jeff's internal-writing voice: direct, mission-linked, no filler.
- **Draft only — do NOT send.** Hand Jeff the draft + the channel, let him send. (Approval threshold: never send a message on Jeff's behalf without explicit go.)

Then close:
> "Anything else — decision log entry, Growth Log update, or prep for the next meeting?"

---

## KEY CONSTANTS

| Constant | Value |
|----------|-------|
| Meeting record store | **Google Docs** — `Meeting Notes` Drive folder (create-only; one Doc per call) |
| Doc name (all calls) | `[YYYY-MM-DD] [Meeting Name]` |
| Interview Doc name | `[YYYY-MM-DD] Interview — [Company] — [Role]` |
| Self-eval | Runs every call; full rubric for interviews, 3-line for others; blunt register; written into the same Doc |
| Growth Log | Apple Notes — `Meeting Intelligence → Growth Log` (append store; stays in Apple Notes) |
| Job-Search Pipeline | Apple Notes — `Job Search → Candidate Pipeline` (append store) |
| Operating manual | Apple Notes — `Meeting Intelligence — Operating Manual` |
| Decision Log note | `CoS — Decision Log` Apple Note (see decision-accountability skill) |
| Todoist project for actions | Ask Jeff if unclear; default to the project most relevant to the meeting topic |

---

## MEETING TYPE REFERENCE

| Meeting | Type | Recorded? | Notes |
|---------|------|-----------|-------|
| Finance Team Weekly | Alignment | No | In-person; manual notes only |
| 1:1 with manager | Alignment / Decision | No | Leadership |
| GLM calls | Informational / Alignment | Varies | Multiple stakeholders |
| Monthly peer call | Informational | Maybe | Personal/professional peer |
| Ad hoc calls | Varies | Varies | Classify at prep time |
| Job search interviews | Decision-making | No | Full rehearsal + debrief |

---

## TRIGGER PHRASES

Run this skill when Jeff says any of:
- "Prep me for [meeting]"
- "Meeting prep" / "prep for [name]"
- "I have a meeting with [person]"
- "Rehearse [meeting]" / "simulate [interview]" / "grill me on this"
- "I'm in the meeting" / "taking notes"
- "Here's my Granola transcript" / "here's what happened"
- "Debrief [meeting]" / "write up [meeting]"
- "Meeting notes for [name]"

---

## GRACEFUL DEGRADATION

| Missing tool | Behavior |
|-------------|---------|
| Google Calendar MCP unavailable | Ask Jeff for meeting details manually |
| Google Drive MCP unavailable | Save to Apple Notes instead, or deliver in chat + offer Word doc via docx skill |
| Apple Notes MCP unavailable | Hold Growth Log / Pipeline updates; deliver in chat and flag they weren't logged |
| Todoist MCP unavailable | List action items in chat; Jeff creates tasks manually |
| Granola transcript unavailable | Accept manual notes or rough bullet input; run the lighter self-eval from recollection |

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-03 | v1.0 — created. Four-phase meeting lifecycle: prep, rehearsal, notes, synthesis. Covers all Jeff's meeting types: Finance Team, 1:1s, GLM, ad hoc, interviews. Apple Notes primary output; flexible. Built from Breunsbach typology and existing csm-pro-v2 meeting-prep-briefing skill. |
| 2026-06-05 | v2.0 — Extracted from CoS plugin into its own standalone **Meeting Intelligence plugin**. Event-triggered (per-meeting), not part of the Monday/Friday button cadence. Kept as ONE skill — the 4 phases are a single sequential lifecycle, not sub-skills (Opus review 2026-06-05). Old cos-plugin copy archived. Phase-build queue (synthesis-as-tail, open-loop detection, growth capture, etc.) tracked in features.json. |
| 2026-06-08 | v2.2 — (1) **Recap distribution folded into core** (Phase 4 Step 5) — offers an attendee-ready recap email/Slack draft; draft-only, never auto-sends. (2) task-extract also folded into core (synthesis already extracts tasks). (3) Record folder renamed `Meeting Intelligence` → **`Meeting Notes`** (avoided collision with plugin source folder). open-loop-sweep + interview-scorecard logged as future separate skills. |
| 2026-06-07 | v2.1 — (1) **Synthesis wired as the tail of note capture** — finishing notes auto-continues into synthesis, closing the back-half-never-runs failure mode. (2) **Blunt self-evaluation added to Phase 4, runs on every call** — full scored interview rubric (6 dimensions, evidence-quote required) + 3-line version for internal calls. (3) **System of record switched from Apple Notes to Google Docs** — one Doc per call (Drive MCP is create-only, no appending). Growth Log + Job-Search Pipeline stay in Apple Notes because they need in-place append. |
