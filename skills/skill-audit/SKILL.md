---
name: skill-audit
description: >
  Audits Jeff's recent Claude sessions to find repeated prompt patterns worth turning into skills, existing skills worth updating, and skills worth retiring. Recommends ONLY changes backed by clear evidence — defaults to "nothing needed." Never creates or edits a skill automatically; proposes and waits. Use when Jeff says "run my skill audit", "skill audit", "audit my skills", or when called from the Monday AI chat review. Biweekly cadence.
version: 1.1
---

# Skill Audit
### Version 1.1 — June 2026
### Platform: Claude Cowork
### Trigger source: Todoist (biweekly, parented under Monday AI chat review)

---

## What This Skill Does

Repeated work is a signal. When Jeff types the same kind of prompt across many
sessions, or hand-walks Claude through the same multi-step workflow again and
again, that pattern should become a skill — so it runs the same way every time
without re-explaining.

This skill reads recent sessions, finds those patterns, and proposes a small,
evidence-backed set of changes:

- **Create** — a recurring workflow that has no skill yet
- **Update** — an existing skill that's drifting, vague, or repeatedly worked around
- **Retire** — a skill that hasn't fired in months or is superseded

**The default answer is "nothing needs to change."** This skill exists to improve
the workflows Jeff actually uses — not to manufacture skills. A thin-evidence
recommendation is worse than no recommendation, because it adds maintenance
surface for no return.

It does **not** create, edit, or delete any skill itself. It proposes; Jeff
decides; skill changes are made separately (skills can't be written from inside a
running session — they're installed via Settings → Skills).

---

## HOW TO RUN

### Step 0 — Set the window

The session tool returns sessions in **recency order, not with timestamps** — so
the window is defined by **session count, not calendar days.**

Default window: **the most recent 30 sessions, OR every session newer than the
last-audit marker (whichever is fewer).** Read the marker first:
`<memory>/skill-audit-last-run.md` — it records the date of the last run and the
ID of the most-recent session seen. Stop pulling once you reach that ID. If the
marker doesn't exist (first run), use the most recent 30.

If Jeff names a window ("last month", "since the deck work"), honor it — map it to
a session count by scanning titles, and say which sessions you treated as the
boundary.

### Step 1 — Pull the corpus

Use the session tools:

1. `list_sessions` (raise the limit to cover the window — e.g. 40–60).
2. **Exclude automated runs from the corpus.** Scheduled-task and agent sessions
   (e.g. "Compass daily scan 6am", scheduled cos-sync runs, any session whose
   title matches a known scheduled task) are not Jeff-typed prompts — counting
   them inflates pattern frequency and produces false Create candidates. Drop
   them before clustering. When unsure whether a session was automated, glance at
   its first message: an automation opens with skill/prompt boilerplate, not a
   human ask.
3. For each remaining in-window session, `read_transcript` (format: `auto`, low
   limit is fine — you're sampling intent, not reading every line). Skip sessions
   still running.

Capture for each session: the **opening ask** (what Jeff wanted), and whether he
**hand-walked Claude through steps** that a skill could have encoded.

### Step 2 — Pull the existing skill inventory

Call `list_skills`. This is the comparison baseline. For every pattern found in
Step 1, the first question is: *does a skill already cover this?* If yes, the
candidate is an **Update**, not a **Create**.

### Step 3 — Cluster and count evidence

Group the openings into patterns. For each pattern, count **how many distinct
sessions** show it. Evidence = distinct sessions, not total mentions in one
session.

Apply the evidence bar (see THRESHOLDS). Below the bar → drop it silently. Do not
report near-misses as "watch items" unless Jeff asks for them — that's noise.

For existing skills, flag a skill as an **Update** candidate only when there's
concrete evidence: Jeff overrode it, corrected it mid-run, or repeatedly did the
thing manually instead of invoking it.

For **Retire**: a skill with no detectable use in the window AND no obvious
seasonal reason to keep it. Be conservative — retiring a rarely-used but correct
skill is a mistake. When unsure, leave it.

### Step 4 — Produce the audit report

```
SKILL AUDIT — [DATE]
Window: [dates] · [N] sessions reviewed · [M] existing skills

CREATE — [N] proposed
  [proposed-skill-name]
  Pattern: [what Jeff keeps asking for]
  Evidence: [X] sessions — [short session titles/dates]
  Why a skill: [what gets encoded / what stops being re-explained]
  Draft trigger phrases: [3–4]

UPDATE — [N] proposed
  [existing-skill-name]
  Problem: [what's drifting — overridden, corrected, worked around]
  Evidence: [X] sessions — [titles/dates]
  Proposed change: [specific edit]

RETIRE — [N] proposed
  [existing-skill-name]
  Reason: [no use in window + no seasonal need]

NO CHANGE
  [one line — e.g. "Everything else is being used as intended."]
```

If nothing clears the bar, the entire report is:

```
SKILL AUDIT — [DATE]
Window: [dates] · [N] sessions reviewed · [M] existing skills

No changes recommended. Existing skills are being used as intended;
no repeated pattern crossed the evidence bar this cycle.
```

That is a complete, successful run. Resist padding it.

### Step 5 — Hand off (no autonomous writes)

For each accepted **Create** or **Update**, the build happens outside this skill:

- Offer to draft the SKILL.md (directory + frontmatter: `name`, `description`,
  `version`) so it's ready to install.
- Remind Jeff that installation is manual: Settings → Skills (cloud/Cowork), and
  for Claude Code a copy at `~/.claude/skills/<name>/SKILL.md`.
- For **Retire**, Jeff removes it via the same Settings → Skills UI.

Never modify the skill store from inside the run.

### Step 6 — Write the last-audit marker

At the end of every run (including a no-change run), write
`<memory>/skill-audit-last-run.md` with: today's date, the ID and title of the
**most-recent session seen** this run, and a one-line summary of the outcome
(e.g. "1 Create proposed: study-guide-builder; skill-audit→v1.1"). This is what
Step 0 reads next time to bound the window. Overwrite the previous marker.

---

## THRESHOLDS

| Condition | Action |
|-----------|--------|
| Pattern appears in **≥ 3 distinct sessions** in the window | Create candidate |
| Pattern in 2 sessions but high-effort each time (long manual walk-through) | Create candidate (note the judgment call) |
| Pattern in 1 session | Drop — not enough evidence |
| Existing skill overridden / corrected in **≥ 2 sessions** | Update candidate |
| Existing skill: no detectable use in window + no seasonal reason | Retire candidate |
| Anything ambiguous | Leave it. Bias to no-change. |

---

## OPTIONAL SECOND PASS — Automations Audit

Only if Jeff asks ("also audit my automations" / "check my scheduled tasks"):

Call `list_scheduled_tasks`. Flag scheduled tasks that are:
- **Noisy** — fire often but produce output Jeff doesn't act on
- **Stale** — haven't meaningfully changed anything in weeks
- **Unclear** — prompt is vague or its output isn't obviously useful
- **Missing an approval step** — takes an action without a confirm gate, where one is warranted

Same evidence bar, same default: it's fine to say nothing needs to change.

---

## TRIGGER PHRASES

- "Run my skill audit" / "skill audit"
- "Audit my skills"
- "What should I turn into a skill"
- "Are there patterns worth systematizing"
- Called from the **Monday AI chat review** (biweekly)

---

## DESIGN NOTES

- **Evidence = distinct sessions.** Five mentions in one session is one data point,
  not five.
- **No-change is the success case.** The skill is judged on precision, not volume.
- **Propose, never write.** Aligns with Jeff's "ask before running" default and the
  platform reality that skills install through Settings, not mid-session.
- **Input source is the session tools** (`list_sessions` / `read_transcript`), not
  a filesystem of session files. ~140 sessions are reachable; sample, don't
  exhaust.

---

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-15 | v1.0 — created. Reads recent sessions via session tools, clusters repeated prompt patterns against the existing skill inventory, proposes Create/Update/Retire with a ≥3-session evidence bar, defaults to no-change. Optional automations-audit second pass. Proposes only — never writes to the skill store. Triggered biweekly from the Monday AI chat review. |
| 2026-06-18 | v1.1 — surfaced by the first live run. (1) Window is now session-count based, not calendar-days — the session tool returns recency order without timestamps, so "14 days" was false precision. (2) Added a last-audit marker (`<memory>/skill-audit-last-run.md`, written in new Step 6) so "since last audit" is computable. (3) Excludes automated/scheduled-task sessions from the corpus before clustering, so automation runs don't inflate pattern frequency. |
