# Meeting Intelligence Plugin — Instructions
### Last updated: 2026-10-12
### Platform: Claude Cowork

---

## What This Plugin Is

An **event-triggered** meeting lifecycle system. One skill, `meeting-intelligence`, covering all four phases of a meeting: prep → rehearsal (optional) → note capture → post-meeting synthesis & follow-up.

Unlike the strategic (Weekly Review) and operational (CoS) plugins, this one is **not** a Monday/Friday button. It fires per-meeting, whenever a calendar event or Granola transcript needs to become structured prep or follow-up.

**Designed for:** any business meeting — client and consulting calls, discovery/sales, partner and vendor meetings, internal 1:1s and team syncs, networking, job-search interviews, and ad hoc calls.

**Framework:** Jeff Breunsbach meeting typology — Informational / Alignment / Decision-making.

---

## Why Its Own Plugin

Decided 2026-06-05 (Opus architecture review). The four phases are **one sequential lifecycle** run against a single meeting and a single Apple Note — they are NOT split into sub-skills. Splitting would add orchestration glue for no gain. But the lifecycle is *event-triggered*, which is a different trigger from the Monday/Friday cadence of `weekly-review` and `cos-sync` — so it earns its own plugin. (Rule: split on independent triggers, not phase count.)

If the SKILL.md grows large, use **progressive disclosure** (one skill that reads phase-specific instruction files on demand) — still one skill, never four.

---

## Place in the Three-Button Architecture

| Track | Plugin | Trigger |
|-------|--------|---------|
| Strategic | Weekly Review (`../weekly-review-plugin/`) | Monday button |
| Operational | CoS (`../cos-plugin/`) | Friday button |
| Daily check-in | Compass (frozen) | parked |
| **Meeting lifecycle** | **Meeting Intelligence (this)** | **per-meeting, on demand** |

It does not consume a "button" — it's invoked by meeting events.

---

## Cross-Plugin Wiring (make explicit as phases are built)

- **Action items → Todoist:** Synthesis phase routes named action items to Todoist via `add-tasks`.
- **Growth log → Apple Notes:** Follow-up captures leadership/delivery/pattern notes to a running Apple Note.
- **Meeting pulse → weekly-review:** A standing section in Monday's `weekly-review` reviews the week's meeting summaries (themes, commitments kept, one behavioral pattern). Requires 2+ weeks of saved summaries first.
- **Open-loop detection:** Reuses the `decision-accountability` pattern (in `../cos-plugin/skills/`) to flag meetings that got a PREP brief but never a synthesis/follow-up.

---

## The Core Design Fix (highest priority)

The known failure mode: **the back half silently never runs.** After a meeting ends, synthesis and follow-up get skipped because attention moves on. The fix is NOT structural — it's lifecycle completion:

1. **Synthesis is the tail of capture.** Finishing NOTE CAPTURE flows automatically into SYNTHESIS + FOLLOWUP in the same run, no separate trigger. (Todoist [TODOIST_TASK_ID])
2. **Prepped-but-never-synthesized detection.** Surface open loops in the meeting pulse. (Todoist [TODOIST_TASK_ID])

These are the heart of the build. Everything else is enhancement.

---

## Where Records Live

- **Per-meeting record:** one Google Doc per call in the `Meeting Notes` Drive folder — `[YYYY-MM-DD] Meeting Name`, or `[YYYY-MM-DD] Interview — [Company] — [Role]`.
- **Running logs** (Growth Log, Job-Search Pipeline, Operating Manual): Apple Notes, because they need in-place append.

---

## Known Gotchas

- **Granola = paste-in only, no MCP.** Transcripts are pasted manually into Claude. No automated pull.
- **In-person meetings are often unrecorded** — captured via manual notes / iPhone, not Granola. Self-eval runs the lighter version from recollection.
- **Calendar PREP trigger** depends on Google Calendar MCP being connected.

---

## File Structure

```
meeting-intelligence-plugin/
├── instructions.md          ← you are here
├── HANDOFF.md               ← goal, decisions, gotchas (multi-session protocol)
├── features.json            ← build tracker, one entry per remaining item
├── skills/
│   └── meeting-intelligence/
│       └── SKILL.md         ← v2.0, the full 4-phase lifecycle
└── reports/                 ← (optional) run logs / growth-log location TBD
```

---

## Build Queue

Tracked in `features.json`. Source of truth for *intent* is the Todoist parent task [TODOIST_TASK_ID] (Meetings Agent) and its 13 subtasks under the AI Agents project. Work one item at a time per the Multi-Session Project Protocol; update `features.json` status and `HANDOFF.md` at each session close.
