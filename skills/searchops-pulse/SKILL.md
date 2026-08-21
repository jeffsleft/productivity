---
name: searchops-pulse
description: "Pull the live SearchOps app (Dashboard + Progress pages) and produce a SearchOps Pulse block for the CoS Weekly Review: new >=8 scores this week, applications aging without follow-up, KR1 pace vs. the ~1.4 apps/wk target, and the Track 2 guardrail check (did an application go out this week). Trigger when the user says 'Run SearchOps Pulse', 'SearchOps pulse', 'job search pulse', or when called automatically from the Friday CoS skill. Also run standalone any time Jeff wants a point-in-time read on the search."
---

# SearchOps Pulse Skill
### Version 1.0 — July 2026
### Platform: Claude Cowork/Chat + Claude in Chrome (browser MCP)

---

## What This Skill Does

A focused, repeatable session that reads the live SearchOps app (Jeff's job-search tool) and produces one output: a **SearchOps Pulse** block, ready to paste into the CoS Weekly Review. Total run time: under 5 minutes. One manual question required (the guardrail check — see Step 4).

This exists so the tool drives the search instead of waiting to be visited — see `[YOUR_PROJECTS_PATH]/recruiting-engine/docs/plan-dual-track-2026-07-14.md`, item W1-CAD.

---

## Key Constants

| Constant | Value |
|---|---|
| App URL | `[YOUR_APP_URL]` |
| Dashboard page | `/` — High-Scoring Roles panel + Follow-ups Due panel |
| Progress page | `/settings/progress` — "Applications" headline (KR1: count of jobs with `applied_at` set) |
| New-score threshold | final score >= 8.0 AND added within the last 7 days |
| KR1 pace baseline | 12 applications as of 2026-07-14 (dual-track plan ground truth) |
| KR1 pace target | ~1.4 applications/week from the 2026-07-14 baseline (needed to hit >=15 by 2026-09-30) |
| Auth | Reuses Jeff's existing authenticated Chrome session. Never attempt to log in or enter the app password. |

---

## STEP 1 — Open the App

Use the Claude in Chrome connector to navigate to the app URL above. If the page renders `/login` (not already authenticated), **stop** — do not attempt to sign in or ask for the password. Tell Jeff the app session isn't authenticated and skip to Step 5 with the SearchOps section flagged as unavailable this week.

---

## STEP 2 — Read the Dashboard (`/`)

**New >=8 scores this week:** Read the "High-Scoring Roles" panel. For each row, note the score and the added/found date. Flag any row where score >= 8.0 and the date falls within the last 7 days. If none qualify, the section is clean.

**Applications aging without follow-up:** Read the "Follow-ups Due" panel directly — the app has already computed this (jobs in Applied/Outreach where `applied_at` is past the due threshold, no follow-up logged since, and any snooze has expired). List each by company + days since applied. If the panel is empty, the section is clean.

---

## STEP 3 — Read Progress (`/settings/progress`)

Read the "Applications" headline number (this is KR1: total jobs with `applied_at` set, not just current-stage `applied`).

Compute expected pace: `expected = 12 + 1.4 * weeks_since_2026-07-14` (round to nearest whole number). Compare the live headline against `expected`:
- At or above expected → **on pace**
- Below expected → **behind by [expected - actual]**

---

## STEP 4 — Guardrail Check (one question, manual)

The app's UI does not expose a per-week "applications sent" breakdown, so ask Jeff directly — this is the same discipline the parent Friday CoS skill already uses for context that lives only in Jeff's head (recurring org work, family updates):

*"Did an application go out this week?"*

This answers the **Track 2 guardrail**: per the dual-track plan, product/showcase work (Wave 3: service refactor, templates, staleness flag, public sync) is only authorized in weeks where >=1 application went out. If the answer is no, say so plainly in the output — it blocks Wave 3 work for the week.

---

## STEP 5 — Output: SearchOps Pulse Block

**Format:** Ready to paste directly into the CoS Weekly Review, after the Behavioral Call-Out (and before or after the CA Move Pulse block — either order is fine).

```
---
SearchOps Pulse — [DATE]

🎯 New >=8 scores this week
- [Company] — [score] — added [date]
(or "None this week")

📮 Applications aging without follow-up
- [Company] — applied [date], [N] days ago, 0 follow-ups logged
(or "Clean — nothing overdue")

📈 KR1 pace: [N] applications total vs. ~[expected] expected (~1.4/wk from 7/14 baseline of 12) — [on pace / behind by X]

🚦 Track 2 guardrail: [✅ application went out this week — Wave 3 work authorized / ❌ no application this week — Wave 3 work not authorized]
---
```

If the app session wasn't authenticated (Step 1), output instead:
```
---
SearchOps Pulse — [DATE]

⚠️ Skipped — app session not authenticated. Log into [YOUR_APP_URL] in Chrome and re-run.
---
```

---

## Known Limitations (July 2026)

- **No per-week applied-date view in the app UI.** No template currently renders `applied_at` as a readable list, so the guardrail check (Step 4) is answered by Jeff directly rather than scraped. If a future SearchOps build surfaces this (e.g., a "recent applications" list), switch Step 4 to read it instead of asking.
- **"New this week" is read, not queried.** The Dashboard's High-Scoring Roles panel shows top 6 roles >=7.0, not a dedicated ">=8 this week" filter — this skill filters visually from what's rendered. If the panel doesn't surface a role that would otherwise qualify (e.g., it's ranked outside the top 6), it will be missed. Not expected to matter at current volume (~3-5 jobs/week discovered per the 2026-07-14 baseline).
- **KR1 pace math is a straight-line target**, not adjusted for the actual W1-T triage outcome or discovery velocity. Treat "behind pace" as a prompt to look closer, not a hard verdict.
- **Requires Claude in Chrome connector** with an already-authenticated session in Jeff's Chrome. This skill never handles the app password.

---

## Integration with Friday CoS Skill

The SearchOps Pulse is called automatically at the end of Output 1 in the Friday CoS skill, alongside (not replacing) the CA Move Pulse. When run inline:
- Skip any pull already done this session if the data is still in context
- Produce the Step 5 block only, appended directly after the Behavioral Call-Out (or after CA Move Pulse if both run)

When run standalone, produce the same block as a point-in-time check.

---

## Session Trigger Phrases

Run this skill when the user says any of:
- "Run SearchOps Pulse"
- "SearchOps pulse"
- "Job search pulse"
- "How's the search tracking?"
- "Where are we on SearchOps?"
- Run standalone or call it from `cos-sync` (`friday-cos-review` was deleted 2026-08-14 — nothing calls this automatically anymore)

---

## Changelog

| Date | Change |
|---|---|
| 2026-07-16 | v1.0 — created per dual-track plan W1-CAD |
