---
name: open-loop-sweep
description: >
  Scans Jeff's Google Drive Meeting Notes folder for dropped commitments: (1) action items from meeting Docs that are still unchecked/open, flagged by age; (2) meetings that appear to have been prepped but never synthesized. Use when Jeff says "what did I drop?", "open loop sweep", "check my open commitments from meetings", "any dropped meetings?", or "what fell through the cracks?" Cross-references with Todoist where possible.
version: "1.0"
---

# Open Loop Sweep Skill
### Version 1.0 — June 2026
### Part of: Meeting Intelligence Plugin
### Platform: Claude Cowork + Google Drive MCP + Todoist MCP

---

## What This Skill Does

Scans across all saved meeting Docs in Google Drive and surfaces two categories of dropped work:

1. **Unclosed action items** — Jeff committed to something in a meeting synthesis, and it's still marked `[ ]` (unchecked) in the Doc, with no corresponding open Todoist task.
2. **Prepped-but-not-synthesized meetings** — a meeting had a PREP brief but no MEETING NOTES / SYNTHESIS Doc exists in the folder.

This runs as an on-demand sweep, not a background monitor. Trigger it when you feel like something slipped, or as a weekly hygiene check.

**Trigger phrases:**
- "What did I drop?"
- "Open loop sweep"
- "Check my open commitments from meetings"
- "Any dropped meetings?"
- "What fell through the cracks?"
- "Run an open loop check"

---

## STEP 1 — Pull All Meeting Docs from Google Drive

Search the `Meeting Notes` folder for all Docs:

```
search_files: title contains '2026' and mimeType = 'application/vnd.google-apps.document'
```

Read each Doc. For each Doc, extract:
- Doc title (date + meeting name)
- All ACTION ITEMS sections — specifically, any item with `[ ]` (unchecked) vs. `[x]` or `[✓]` (checked)
- The owner of each action item (Jeff vs. someone else)
- Due date if specified in the item

Track only **Jeff's** unchecked action items. Items owned by others are noted separately.

---

## STEP 2 — Age and Prioritize Unclosed Items

For each unchecked Jeff-owned action item, calculate days since the meeting:

| Age | Label |
|-----|-------|
| 0–3 days | Normal — not yet flagged |
| 4–7 days | Watch — coming due |
| 8–14 days | Overdue — flag clearly |
| 15+ days | Dropped — flag prominently |

Only surface Watch / Overdue / Dropped items in the output. Items from the last 3 days are noted as a count only ("N items from this week, not yet due").

---

## STEP 3 — Cross-Reference with Todoist (if available)

For each Overdue or Dropped item, search Todoist for a matching open task:

```
find-tasks: searchText = "[key words from action item]"
```

**If a matching open Todoist task exists:** label the action item as `✅ In Todoist` — it's tracked, just not closed in the Doc. Not a true drop.

**If no match in Todoist:** label as `⚠️ NOT IN TODOIST` — this is the actual open loop.

---

## STEP 4 — Check for Prepped-But-Not-Synthesized Meetings

This is a best-effort check. Search for Docs with PREP/BRIEF content but no SUMMARY or MEETING NOTES section:

```
search_files: fullText contains 'MEETING BRIEF' and NOT fullText contains 'SUMMARY'
```

Also check: any Docs with only a prep brief title structure (e.g., `[YYYY-MM-DD] Prep — [Meeting Name]`) that have no corresponding synthesis Doc (`[YYYY-MM-DD] [Meeting Name]` with a SUMMARY section).

Flag any matches as: *"Meeting was prepped but synthesis was never run."*

Note: This detection is best-effort. If the PREP brief was delivered only in chat (not saved to Drive), it won't appear here. The core failure mode this catches is Docs that were started but abandoned.

---

## STEP 5 — Produce the Sweep Report

```
OPEN LOOP SWEEP — [YYYY-MM-DD]
Docs scanned: [N]
Period covered: [earliest Doc date] to [latest Doc date]

─────────────────────────────────────────────
⚠️ OPEN LOOPS — JEFF'S UNCLOSED ACTION ITEMS
─────────────────────────────────────────────

DROPPED (15+ days):
• [Action item text] — from [Doc title] ([date]) — [N] days ago
  Status: NOT IN TODOIST ← true open loop
  OR: ✅ In Todoist as "[task name]" ← tracked, close the Doc item

OVERDUE (8–14 days):
• [Action item text] — from [Doc title] ([date]) — [N] days ago
  Status: [NOT IN TODOIST / ✅ In Todoist]

WATCH (4–7 days):
• [Action item text] — from [Doc title] ([date]) — [N] days ago
  Status: [NOT IN TODOIST / ✅ In Todoist]

This week (0–3 days): [N] items — not yet flagged.

─────────────────────────────────────────────
🔴 PREPPED BUT NEVER SYNTHESIZED
─────────────────────────────────────────────

[If any found]
• [Doc title] — [date] — PREP exists, no SUMMARY/SYNTHESIS found

[If none found]
All prepped meetings have a corresponding synthesis Doc. ✓

─────────────────────────────────────────────
OTHERS' OPEN ITEMS (FYI — not your loop)
─────────────────────────────────────────────

[List action items owned by others that are still open in meeting Docs, in case a
follow-up nudge is appropriate. E.g., "Ian — send Asana board" from 2026-06-12, 12 days ago.]

─────────────────────────────────────────────
SUMMARY
─────────────────────────────────────────────

True open loops (Jeff-owned, not in Todoist): [N]
Tracked in Todoist (just not closed in Doc): [N]
Others' items worth nudging: [N]
Prepped-but-not-synthesized: [N]
```

---

## STEP 6 — Offer Next Actions

After presenting the report:

> "For each true open loop — want me to:
> a) Create a Todoist task now?
> b) Mark it as no longer relevant (add a note to the Doc)?
> c) Skip for now?
>
> For items already in Todoist, I can mark the Doc item checked if you confirm they're on track."

Handle each item Jeff responds to. Do not bulk-act without confirmation.

---

## GRACEFUL DEGRADATION

| Missing | Behavior |
|---------|---------|
| Google Drive MCP unavailable | Ask Jeff to paste action items from recent Docs manually; run the triage against what he provides |
| Todoist MCP unavailable | Present open loops from Drive only; skip the Todoist cross-reference step; note the gap |
| Fewer than 4 Docs in folder | Run the sweep on what exists; note "more data = more reliable pattern detection" |
| Action items not in `[ ]` format | Scan for lines starting with bullet + verb + owner name as a fallback; flag as "manually parsed" |

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-24 | v1.0 — created. Prereq met: 5 Docs in Meeting Notes Drive folder (2026-06-09 through 2026-06-23). Part of Meeting Intelligence Plugin. |
