---
name: daily-scan
description: "A 5am anticipatory sweep of Gmail, Google Calendar, and Todoist. Catches what's landing today before Jeff would have thought to ask — inbound emails needing a yes/no, calendar items unanswered or stacked, tasks due or overdue. Notify-only: posts a 3-line brief to Slack #chief-of-staff and takes no actions. Part of the Compass plugin (anticipatory layer). Trigger when Jeff says 'run the daily scan', 'what's landing today', 'morning scan', or when called by the 5am scheduled task."
version: 1.0
---

# Daily Scan Skill
### Version 1.0 — June 2026
### Part of: Compass Plugin (anticipatory layer)
### Platform: Claude Cowork + Gmail MCP + Google Calendar MCP + Todoist MCP + Slack MCP

---

## What This Skill Does

The anticipatory layer. Every other skill in both plugins is **pull** (Jeff triggers it) or **scheduled cleanup**. This one catches problems *before* Jeff would have thought to ask — the email sitting 3 days needing a yes/no, the calendar invite he never answered, the stack of three hard meetings on one afternoon, the task quietly going overdue.

It is **strictly notify-only.** It reads. It posts a 3-line brief to Slack `#chief-of-staff`. It takes no actions: no drafts, no task creation, no calendar responses, no archiving. Jeff reads the brief and decides. This matches the "ask before running" default — the scan never acts on Jeff's behalf.

**Why notify-only (v1.0):** Trust is built before leverage is granted. Once Jeff has run this for a few weeks and the signal is clean, a future v2.0 can add an opt-in "draft replies" mode. Not before.

---

## Run Mode

| Mode | Behavior | Status |
|------|----------|--------|
| **Notify-only** | Scan → 3-line Slack brief → stop | **Active (v1.0)** |
| Notify + draft | Scan → brief → draft (not send) replies for yes/no items | Future v2.0 (opt-in) |
| Notify + auto-task | Scan → brief → auto-create Todoist tasks | Not planned (clutter risk) |

---

## HOW TO RUN

Confirm today's date and the current time at session open. The scan window is "today" (now → end of day) plus a short lookback for aging inbound.

Run the three pulls in parallel. Each has a strict scope to keep the brief short and the signal high.

### Pull 1 — Gmail (inbound needing attention)

Use `search_threads`. Two queries:

1. **Needs a reply, aging:** `in:inbox is:unread newer_than:4d -category:promotions -category:social -category:updates`
   - Surfaces unread personal/work mail from the last 4 days. Discard newsletters and notifications.
2. **Awaiting my reply:** `in:inbox is:unread newer_than:7d -in:sent -category:promotions -category:social -category:updates`
   - Catches threads that have aged past 3 days with no reply from Jeff.

For each candidate, judge: does this plausibly need a **yes/no or a decision from Jeff?** Keep only those. Cap at the 3 most important. Note sender + one-line subject + age.

**Mercy Ships note:** Mercy Ships email is Outlook (locked down, no MCP). This scan only sees Jeff's Gmail (personal + job search). Do not claim coverage of Mercy Ships mail.

### Pull 2 — Google Calendar (today's shape)

Use `list_events` with `startTime` = now, `endTime` = end of today. Detect three things:

1. **Unanswered invites** — events where Jeff's response status is `needsAction`.
2. **Stacking** — three or more substantive meetings in one afternoon, or back-to-back blocks with no gap. This is the "you've got three hard things stacked, move one" read a human CoS gives.
3. **Conflicts** — overlapping events.

Surface only the single most important calendar flag. If the day is clear or normal, say "calendar clear."

### Pull 3 — Todoist (what's due, what's slipping)

Use `find-tasks-by-date` with `startDate` = today, `overdueOption` = `include-overdue`. Surface:

1. **Overdue P1/P2** — anything important already past due.
2. **Due today, high-stakes** — P1/P2 due today.

Cap at the 2 most important. Respect [PARTNER]'s tasks (UID `[PARTNER_TODOIST_UID]`) — surface as a coordination prompt only, never as Jeff's action.

---

## OUTPUT — 3-Line Slack Brief

Post to `#chief-of-staff` via Slack MCP. **Three lines, hard cap.** Lead with what needs Jeff, not a recap. Format:

```
☀️ Daily Scan — [Mon, Jun 8]
📧 [Most important inbound, sender + ask + age] — or "inbox clear"
📅 [Top calendar flag] — or "calendar clear"
✅ [Top task flag] — or "nothing overdue"
```

Example:
```
☀️ Daily Scan — Mon, Jun 8
📧 Recruiter @ Acme waiting 3d on interview availability — yes/no
📅 3 meetings stacked 1–4pm; the GLM call has no prep — consider moving one
✅ P1 "Confirm Auburn movers" overdue 2 days
```

If everything is clear:
```
☀️ Daily Scan — Mon, Jun 8 — All clear. No inbound needing you, calendar normal, nothing overdue.
```

**Get the channel ID** via `slack_search_channels` for `#chief-of-staff` if not already known. Confirm posting only if run manually outside the scheduled task; the scheduled 5am run posts automatically (this is the one autonomous action, and it's a read-only notification — no state changes).

---

## SCHEDULE

Runs daily at **5:00am** via a scheduled task. The scheduled task prompt is simply: *"Run the daily-scan skill."*

To pause: disable the scheduled task. To change cadence: update it via the scheduling tool.

---

## THRESHOLDS

| Signal | Surface if |
|--------|-----------|
| Gmail thread | Unread, <7 days, plausibly needs a yes/no, not a newsletter |
| Calendar | Unanswered invite, 3+ stacked meetings/afternoon, or a conflict |
| Todoist | P1/P2 overdue, or P1/P2 due today |

Brief shows at most: 3 email lines collapsed to 1, 1 calendar line, 1 task line. **Total: 3 lines.** Brevity is the product. If there's more, it can wait for the weekly review.

---

## GRACEFUL DEGRADATION

| Missing tool | Behavior |
|-------------|----------|
| Gmail MCP unavailable | Skip Pull 1; note "📧 (Gmail unavailable)" in the brief |
| Calendar MCP unavailable | Skip Pull 2; note "📅 (Calendar unavailable)" |
| Todoist MCP unavailable | Skip Pull 3; note "✅ (Todoist unavailable)" |
| Slack MCP unavailable | Deliver the 3-line brief in chat instead; flag that it wasn't posted |

---

## TRIGGER PHRASES

- "Run the daily scan"
- "What's landing today"
- "Morning scan"
- "Anything I'm missing today"
- Called automatically by the 5am scheduled task

---

## DESIGN NOTES

This skill exists because the rest of the CoS system is reactive. It closes the single biggest gap between this system and a human Chief of Staff: a human watches your inbound and your calendar without being asked. The 3-line cap is a feature — a long brief gets skimmed and ignored. If it's not worth 3 lines at 5am, it's not a 5am problem.

The deliberate limitation — notify-only, Gmail-only (no Mercy Ships Outlook) — is honest scope, not a placeholder. Widen it only after the signal proves clean.

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-04 | v1.0 — created. Notify-only 5am sweep of Gmail + Calendar + Todoist → 3-line Slack brief to #chief-of-staff. No actions taken. |
