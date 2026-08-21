---
name: meeting-task-extract
description: >
  Extract decisions, action items, open questions, and parking-lot items from any block of text — meeting summary, rough notes, email thread, document, or Granola transcript — and route selected items to Todoist. Use when Jeff says "extract tasks from this", "pull out the action items", "what do I need to do from this?", "triage this summary", or pastes a block of text that needs structured processing. Distinct from meeting-intelligence (which runs the full lifecycle for a single meeting). Use this skill for ad hoc extraction from any source.
version: "1.0"
---

# Meeting Task Extract
### Version 1.0 — June 2026
### Part of: Meeting Intelligence Plugin
### Platform: Claude Cowork + Todoist MCP + (optional) Apple Notes MCP

---

## What This Skill Does

Takes any block of text and extracts four categories of actionable content:

| Category | What it captures |
|----------|-----------------|
| **Decisions** | Choices that were made — record for the log |
| **Action Items** | Things someone needs to do — owner + due date if present |
| **Open Questions** | Unresolved questions that need follow-up |
| **Parking Lot** | Ideas or topics that came up but weren't decided — worth tracking |

Presents the extract to Jeff for review, then routes selected action items to Todoist and optionally logs decisions to the CoS Decision Log Apple Note.

**This is not a full meeting lifecycle.** For prep, rehearsal, and synthesis with self-eval, use `/meeting-intelligence`. This skill is for ad hoc extraction from any source — existing summaries, pasted email threads, rough notes, external documents.

---

## STEP 1 — Accept Input

Accept the text inline (paste into chat) or by reference:
- "Here's the summary from [meeting]"
- "Paste" + block of text
- "Pull from my Apple Notes — [note name]" → read via Apple Notes MCP

If no text is provided, ask:
> "Paste the text you want me to extract from — or name an Apple Note and I'll pull it."

Note the source and approximate date if provided. If the source is a meeting, note whether the full meeting-intelligence lifecycle was already run — if it was, this skill is handling overflow, not a duplicate.

---

## STEP 2 — Extract

Parse the text and produce a structured extract. Do not invent items that aren't in the text. If a category has nothing, leave it blank — do not force it.

```
EXTRACT — [Source / date if known]

─────────────────────────────────────────────
DECISIONS
─────────────────────────────────────────────
1. [Decision made] — [owner if stated]
2. ...
(blank if none)

─────────────────────────────────────────────
ACTION ITEMS
─────────────────────────────────────────────
1. [ ] [Action] — [owner] — due [date / "TBD"]
2. [ ] [Action] — [owner] — due [date / "TBD"]
...
(blank if none)

─────────────────────────────────────────────
OPEN QUESTIONS
─────────────────────────────────────────────
1. [Question that wasn't resolved]
2. ...
(blank if none)

─────────────────────────────────────────────
PARKING LOT
─────────────────────────────────────────────
1. [Idea / topic flagged but not acted on]
2. ...
(blank if none)
```

**Extraction rules:**
- Action items must have a verb (not just a topic). "Budget review" is not an action item; "Review Q3 budget with Heri by Friday" is.
- If an owner isn't stated, assign "Jeff" only if the context makes it unambiguous — otherwise leave owner blank and flag it.
- Pull due dates from the text exactly as stated; convert relative references ("by end of week", "next Friday") to absolute dates using today's date.
- If the text is a Granola transcript rather than a processed summary: extract from the full dialogue, not just explicit "action item" statements — surface commitments made mid-conversation.

---

## STEP 3 — Review + Route

After presenting the extract, ask:

> "Which action items do you want in Todoist? Say 'all', 'none', or list them by number. For each one I'll confirm the project before creating."

Wait for Jeff's answer. Do not create Todoist tasks without confirmation.

### Creating Todoist tasks

For each confirmed action item:
- **Content:** the action description (clean, imperative)
- **Project:** ask if not obvious from context; default to the project most relevant to the topic (e.g., Mercy Ships tasks → Finance Ops, job search tasks → Job Search, move tasks → Return to Auburn)
- **Due date:** use the extracted date if present; otherwise leave blank unless Jeff specifies
- **Description:** source name + date (e.g., "From: Heri 1:1 — 2026-06-29")

Confirm the full list before writing:
> "I'll create [N] tasks: [list]. Good to go?"

### Logging decisions

After routing action items, offer for any decisions extracted:
> "Want any of these decisions logged to your CoS Decision Log Apple Note?"

If yes, append each decision in this format to the `CoS — Decision Log` Apple Note:
```
[YYYY-MM-DD] [Decision] — [owner if applicable] — From: [source]
```

Only append — never overwrite the existing note.

---

## STEP 4 — Open Questions handling

After action items, offer:
> "I have [N] open questions. Want me to add any of these to Todoist as research tasks, or note them somewhere?"

Options:
- **Todoist** — create as tasks with "Clarify:" prefix, owner = Jeff
- **Apple Note** — append to a note Jeff names (or a new one)
- **Skip** — Jeff handles manually

---

## GRACEFUL DEGRADATION

| Missing | Behavior |
|---------|----------|
| Todoist MCP unavailable | List action items in chat; Jeff creates manually |
| Apple Notes MCP unavailable | Skip decision logging; note the gap |
| Text is very short (< 3 sentences) | Run the extract but note: "Not much to work with — I'll surface what I can." |
| Text is a raw Granola transcript (unprocessed) | Note: "This looks like a raw transcript. I'll extract from the full dialogue — may take a moment." Then extract from commitments made mid-conversation, not just explicit action item statements. |

---

## TRIGGER PHRASES

Run this skill when Jeff says any of:
- "Extract tasks from this"
- "Pull out the action items"
- "What do I need to do from this?"
- "Triage this summary"
- "Extract from [note/meeting name]"
- Pastes a block of text and asks "what needs to happen?"

---

## INTEGRATION NOTES

- **`meeting-intelligence`**: This skill handles overflow or ad hoc extraction. If the full lifecycle (prep → rehearsal → notes → synthesis) is needed, use `/meeting-intelligence` instead.
- **`open-loop-sweep`**: If extraction reveals items from a meeting that never got synthesized, flag them for the sweep skill.
- **`walk-and-talk-interviewer`**: If action items surface Todoist tasks that need clarification, route them to the Walk & Talk interviewer.

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-29 | v1.0 — created. Standalone extraction skill for ad hoc text. Part of Meeting Intelligence Plugin. Unblocked from features.json after 5+ real meeting runs (threshold judged sufficient). |
