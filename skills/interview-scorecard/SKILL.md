---
name: "interview-scorecard"
description: "Trends Jeff's interview and networking-with-intent self-eval scores across Google Drive meeting Docs and project-folder debriefs. Use when Jeff says \"how am I trending in interviews?\", \"interview scorecard\", \"show me my interview pattern\", \"what am I consistently weak on?\", or \"pull my scorecard.\" Aggregates 6-dimension scores across all logged calls, surfaces recurring weak spots, momentum, and one prioritized coaching focus."
---

# Interview Scorecard Skill
### Version 1.7 — September 2026
### Part of: Meeting Intelligence Plugin
### Platform: Claude Cowork + Google Drive MCP + (optional) Todoist MCP

---

## What This Skill Does

The Meeting Intelligence skill scores ONE call. This skill trends MANY: it aggregates the per-call self-evaluations across every interview or networking-with-intent call and shows which dimensions are stuck, which are improving, and what to prioritize before the next conversation.

**This file is method only, and company-agnostic.** Jeff's call history, patterns and company-specific rulings live in the data file below. Never write company names, people, call-by-call scores or project paths into this skill.

**Data file:** `~/Documents/Claude/Projects/Interviews/interview-scorecard-data.md` (the connected **Interviews** folder).
- Read it at the start of every run, and update it at the end.
- If it can't be reached, say so and ask Jeff to connect the folder. Don't rebuild history from memory.

**Trigger phrases:**
- "How am I trending in interviews?"
- "Interview scorecard"
- "Show me my interview pattern"
- "What am I consistently weak on?"
- "Pull my scorecard"
- "How many interviews have I done?"

---

## ⚠️ STEP 0 — THE SOURCES OF TRUTH

1. **Drive and the project folders are the source of truth.** The snapshot table in the data file is a convenience copy, and it has gone stale before. If it disagrees with Drive or the project folders, they win. Update the snapshot after any run where they disagree.
2. **Check the project folders, not just Drive.** Panel, interview and founder-round debriefs are often written as `.md` or `.docx` files into a project folder and never reach the Meeting Notes Drive folder. The data file lists the current locations.
3. **Never score a call twice.** Before scoring, search for an existing debrief. Two conflicting records are worse than none. If one exists, reconcile with it rather than replacing it.
4. **Check for correction Docs.** The Drive connector can't edit a Doc in place, so corrections are filed as a separate `YYYY-MM-DD Correction — …` Doc next to the original. The correction wins.
5. **Apply the data file's company-specific standing rulings** when scoring a call with that company.

---

## What Counts as a Scoreable Call

Only calls where the **full 6-dimension rubric** was run:
- **Job search interviews:** recruiter screen, hiring manager, panel, exec.
- **Networking-with-intent calls:** Jeff was actively exploring a role, or positioning himself with someone who could refer or vouch for him.
- **Founder or principal conversations inside a live process,** even when framed as a catch-up. If the other party can decide, it's scoreable.

**Not scored:** internal work meetings, finance-team calls, and peer check-ins with no career positioning. These get the 3-line eval in the meeting Doc, not the rubric.

The meeting-intelligence skill labels the two kinds in the Doc: `SELF-EVALUATION (FULL RUBRIC)` vs. `SELF-EVALUATION` (3-line).

---

## The Six Dimensions — and What They Actually Measure

Five dimensions are about *what was said*, so they hold up on a cleaned speech-to-text transcript:
- narrative clarity
- evidence & specificity
- question handling
- fit & differentiation
- curiosity

Score each 1–5 (1 = hurt you, 3 = competent, 5 = strong differentiator), tied to a moment in the transcript.

**Scoring rules for these five:**
- **Before scoring a line as a question Jeff didn't answer, read the lines before and after it.** Sometimes the other person describes something their team is still working out, then extends it to Jeff ("what does success look like for this role… I'd want that for you too"). That's an invitation, not a question waiting for a live answer.
- **Size evidence coaching to the target company.** Big-company metrics don't connect at a small company, so don't prescribe them as the fix. Coach toward proof that matches the target company's actual problems.
- **Note whether the interviewer is a stranger or someone Jeff already knows.** His register differs between the two. For acquaintance rounds, explain the Fit & differentiation score, don't just record it.

### Dimension 6 — Verbal discipline (tic density)

This used to be "executive presence." A transcript can't hear tone or see the room, so it was replaced with something a transcript *can* measure honestly.

**What it measures:** the density of fillers, hedges, weakeners and self-diminishing tics per 1,000 words. It's a countable stand-in for composure: answers composed live fill up with "kind of," "I guess" and "or whatever," and prepared answers don't.

**Count these markers:**
- **Fillers** *(raw or verbatim transcripts only)*: um, uh, you know, like (as filler), I mean
- **Hedges / weakeners**: kind of, sort of, I guess, maybe, probably, just, a little bit, whatever, or something, that kind of thing, I think (when it softens rather than asserts)
- **Apology / self-diminishing tics**: sorry, I don't want to bore you, I don't know, this is probably wrong, that might sound weird, I can't remember, I don't mean this in an offensive way
- **Validation-seeking closers**: "is that what you're looking for?", "does that make sense?", "I hope". These cluster at the end of long answers and point to answer *length*, not low confidence.

**Do not count:**
- **Stutter double-renders** ("if all if all," "I think I think"). These are transcription artifacts, not something Jeff said.
- **Self-ruleouts** ("maybe you don't need this role"). This is deliberate strategy on Jeff's part, locked 2026-08-26. Score it neutral and never re-flag it. If a specific instance costs him something concrete, log that instance on its own merits.
- **Words used for their real meaning.**
  - "just" appears 13–25 times in a typical transcript. Most uses are assertive ("we just have to build it") or spoken in a quoted or hypothetical voice ("I just want a walled garden").
  - About a third of "I think" uses are assertive too.
  - Read each one; don't count them automatically.

### ⚠️ Two metrics, only one scored

| Metric | What it is | Use |
|---|---|---|
| **Hedge density**: hedges + weakeners + apology tics + validation closers, per 1,000 words | Works on every transcript type | **The scored metric** |
| **Raw filler density**: um / uh / you know / filler "like" / "I mean", per 1,000 words | Needs a verbatim transcript | **Logged, not scored,** until enough raw calls exist to re-derive the bands |

**Score bands (hedge density only):**
- 5/5 — under 5
- 4/5 — 5–10
- 3/5 — 10–18
- 2/5 — 18–28
- 1/5 — over 28

Applying these bands to a raw filler count drags scores down about two bands.

**Reporting:**
- Cite the band and the markers with counts (e.g., "kind of ×3, whatever ×4"), so the score can be audited.
- Report both metrics; score only hedge density.
- **If the count is within about 1 point of a band boundary, run a sensitivity check:** drop the most debatable marker class, recompute, and state whether the band holds.

---

## STEP 1 — Pull All Scoreable Docs

The Meeting Notes Drive folder ID is `[YOUR_MEETING_NOTES_FOLDER_ID]`.

```
search_files: parentId = '[YOUR_MEETING_NOTES_FOLDER_ID]'
```

The folder is small enough to list in one page. Not every scoreable Doc uses the heading `SELF-EVALUATION (FULL RUBRIC)`; some use `Scored Rubric` or `Scorecard`. If the count looks short, search on the dimension names.

**Then check the project folders listed in the data file.**

From each Doc, extract:
- title
- call date and call type
- all 6 scores
- weighted read, two things to sharpen, and one thing that worked
- whether the transcript was raw, cleaned or partially cleaned
- whether the recording was complete or partial
- the company, and whether the interviewer was a stranger or someone Jeff already knows

A blank dimension is `—` and is excluded from that dimension's average.

---

## STEP 2 — Build the Scorecard

```
INTERVIEW SCORECARD — Jeff Beaumont
Generated: [YYYY-MM-DD]
Calls logged: [N]

DIMENSION SCORES BY CALL
[Dimension]                 [Call 1]  [Call 2]  ...  AVG
Narrative clarity             X/5       X/5          X.X
Evidence & specificity        X/5       X/5          X.X
Question handling             X/5       X/5          X.X
Verbal discipline (tics)      X/5       X/5          X.X
Fit & differentiation         X/5       X/5          X.X
Curiosity / questions         X/5       X/5          X.X

TREND READ
  Stuck (avg ≤ 3.0, no upward movement)
  Improving (upward across 2+ calls)
  Consistent strength (avg ≥ 4.0)
  Regressing (was locked high, now falling) — flag these hardest

RECURRING SHARPEN-POINTS
  [Pattern] — appeared in [N] of [N] calls

COACHING FOCUS — ONE THING TO PRIORITIZE
  [Highest-ROI fix. Ground it in evidence. Be blunt. One paragraph.]

WEIGHTED READ HISTORY
  [YYYY-MM-DD] [Call]: "[verdict]"
```

**Then update the data file:** the snapshot table, transcript provenance, the pattern, and a dated history line. Company-specific rulings go in the data file, never in this skill.

---

## STEP 2.5 — Push to SearchOps (interview_sessions sync)

SearchOps is Jeff's job-search tracker (`recruiting-engine`), and it has an `interview_sessions` table. Push every scoreable call from STEP 1 on every run. The endpoint dedupes on `(company, date, mode)`, so re-sending a call that's already there is safe.

```
POST [YOUR_SEARCHOPS_URL]/api/sync/interview-session
Headers:
  Content-Type: application/json
  X-Requested-With: XMLHttpRequest
  Authorization: Bearer <SearchOps sync credential>
Body:
{
  "company": "<company or org name>",
  "date": "<YYYY-MM-DD>",
  "mode": "prep" | "live" | "debrief",
  "questions_covered": ["<question text>", ...],
  "self_eval_scores": {"narrative_clarity": <1-5>, "evidence_specificity": <1-5>, "question_handling": <1-5>, "verbal_discipline": <1-5>, "fit_differentiation": <1-5>, "curiosity_questions": <1-5>},
  "notes": "<weighted read + two things to sharpen + one thing that worked>",
  "transcript": "<full transcript if available, else omit>"
}
```

**Mode:**
- Interviews and networking-with-intent calls → `"live"`.
- Prep and mock interviews → `"prep"`.
- Debrief-only notes with no live call → `"debrief"`.

**Scores:** leave out any dimension scored `—`; don't send it as null. `verbal_discipline` takes the hedge-density score.

**Credential:** the SearchOps app password is never stored in this skill. Don't ask Jeff to paste it into chat. If it's read from a file, read it inside the shell and pipe it straight into the request; never echo, print or log it.

**Network:** test with an empty POST first. A 403 *from the proxy* at CONNECT means the domain isn't on the organization's network allowlist; it isn't a server problem.

**Response:** `{"status": "success", "session_id": <int>}`. Report any non-200 in the scorecard output, but don't hold the scorecard back because of it.

---

## STEP 3 — Offer Next Actions

> "Want me to:
> a) Log the coaching focus to your Growth Log Apple Note so it persists across sessions?
> b) Check if any of the recurring sharpen-points appear as open Todoist tasks already?
> c) Flag the next scheduled interview in Google Calendar so I can prep you with this pattern in mind?"

Only do what Jeff says yes to.

---

## GRACEFUL DEGRADATION

| Situation | Behavior |
|---------|---------|
| Data file unreachable | Say so and ask Jeff to connect the Interviews folder. Build the scorecard from Drive and the project folders only. |
| Fewer than 3 Docs | Present what exists; note the scorecard gains predictive value at 3+. |
| Scores not in table format | Extract from the text; flag as "manually parsed — verify." |
| Google Drive MCP unavailable | Ask Jeff to paste the eval sections; aggregate manually. |
| A dimension is blank | Show as `—`; exclude from that dimension's average. |
| The interviewer asked Jeff almost nothing | Score question handling `—`, not a guessed 3. One soft question isn't a data point. State the judgment call. |
| A line reads like an unanswered question | Read the surrounding lines first; an invitation isn't a missed question. |
| Cleaned transcript only | Score hedge density; note that fillers can't be measured. |
| Partially cleaned transcript | Score hedges; don't compare its raw filler count to fully raw calls. |
| Partial recording | Score it, mark the column, and say so in the trend read. |
| A debrief already exists | Reconcile; don't re-score from scratch. |
| Hedge count near a band boundary | Run the sensitivity check and report both figures. |
| Recording titled with the wrong name | Identify the other party from the content, tag it [Inference], and confirm with Jeff before writing any record. |
| No HTTP-capable tool | Skip the SearchOps sync; mention it once per session. |
| Endpoint blocked by the network proxy (403 at CONNECT) | Skip the sync; tell Jeff once that the SearchOps domain needs adding to the organization's allowlist. |
| SearchOps credential unavailable | Skip the sync, mention it once, and don't ask Jeff to paste the password. |
| SearchOps returns non-200 | Skip that call, continue with the rest, and report the failure count. |
| Company or date unclear for a Doc | Skip the sync for that Doc only; it still counts in the scorecard. |

---

## CHANGELOG

*Method changes only. Data-driven changes are logged in the data file.*

| Date | Change |
|------|--------|
| 2026-06-24 | v1.0 — created. |
| 2026-07-15 | v1.1 — Dimension 6 changed from "executive presence" to "verbal discipline (tic density)." |
| 2026-08-10 | v1.2 — Drive as source of truth; project-folder check; scored hedge metric split from logged raw-filler metric. |
| 2026-08-12 | v1.3 — SearchOps sync (STEP 2.5) with dedup and degradation rows. |
| 2026-09-09 | v1.4 — Founder catch-ups count as scoreable; self-ruleouts moved to exclusions; validation closers counted; "partially cleaned" provenance; band-boundary sensitivity check; `—` for question handling when almost nothing was asked. |
| 2026-09-24 | v1.5 — New apology tic; quoted-voice "just" excluded; mislabeled-recording row. |
| 2026-09-25 | v1.6 — Read-the-surrounding-lines rule; evidence coaching sized to the target company; correction-Doc check; egress-proxy row; never echo a secret read from a file. |
| 2026-09-25 | v1.7 — **Made company-agnostic.** v1.5–v1.6 had written company names, people, paths and one company's rulings into the method. The call history, snapshot, pattern and company-specific rulings now live in the data file (`Interviews/interview-scorecard-data.md`); the skill reads it at the start of each run and updates it at the end. |