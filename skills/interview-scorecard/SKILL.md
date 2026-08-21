---
name: "interview-scorecard"
description: "Trends Jeff's interview and networking-with-intent self-eval scores across Google Drive meeting Docs. Use when Jeff says \"how am I trending in interviews?\", \"interview scorecard\", \"show me my interview pattern\", \"what am I consistently weak on?\", or \"pull my scorecard.\" Reads SELF-EVALUATION (FULL RUBRIC) sections from Meeting Notes Docs in Google Drive, aggregates 6-dimension scores across all logged calls, surfaces recurring weak spots, momentum, and one prioritized coaching focus."
---

# Interview Scorecard Skill
### Version 1.3 — August 2026
### Part of: Meeting Intelligence Plugin
### Platform: Claude Cowork + Google Drive MCP + (optional) Todoist MCP

---

## What This Skill Does

Aggregates the per-interview self-evaluation scores that the Meeting Intelligence skill logs into Google Drive after every interview or networking-with-intent call. The core meeting-intelligence skill scores ONE call; this skill trends MANY — surfacing which dimensions are stuck, which are improving, and what to prioritize before the next conversation.

**Trigger phrases:**
- "How am I trending in interviews?"
- "Interview scorecard"
- "Show me my interview pattern"
- "What am I consistently weak on?"
- "Pull my scorecard"
- "How many interviews have I done?"

---

## ⚠️ STEP 0 — DRIVE IS THE SOURCE OF TRUTH, NOT THIS FILE

**Always rebuild the table from Google Drive. Never generate a scorecard from the KNOWN DATA POINTS block below.**

That block is a convenience snapshot and it has gone stale before. In August 2026 it was found to be three calls behind — it listed five evals "as of 2026-07-15" while Drive held eight. Any scorecard built from the snapshot under-reported the call count and misstated every average.

**If the Drive-derived table disagrees with the snapshot, Drive wins.** After any run where they disagree, update the snapshot in this skill (via `save_skill` with `overwrite: true`) so the drift does not compound.

**Also check the project folder, not just Drive.** Some panel and interview debriefs are written as `.docx` into the relevant project folder (e.g. `[Company]/Interview-Prep/`) and never reach the Meeting Notes Drive folder. These contain full rubric scores and are invisible to a Drive-only search. Before scoring a call, search the project folder for an existing debrief — **scoring a call that already has a scorecard produces two conflicting records, which is worse than no record.** If one exists, reconcile rather than replace.

---

## What Counts as a Scoreable Call

Only calls where the **full 6-dimension rubric** was run. These are:
- Job search interviews (recruiter screen, hiring manager, panel, exec)
- Networking-with-intent calls (calls where Jeff was actively exploring a role or positioning himself to someone who could refer or vouch for him)

**Not scored:** internal Mercy Ships meetings, Finance Team calls, peer check-ins with no career positioning dimension. These get the 3-line eval in the meeting Doc, not the rubric.

The meeting-intelligence skill labels these clearly in the Doc: `SELF-EVALUATION (FULL RUBRIC)` vs. `SELF-EVALUATION` (3-line).

---

## The Six Dimensions — and What They Actually Measure

Five of the six dimensions are about *what was said* and survive a cleaned speech-to-text transcript well: narrative clarity, evidence & specificity, question handling, fit & differentiation, and curiosity. Score those 1–5 as before (1 = hurt you, 3 = competent, 5 = strong differentiator), each tied to a transcript moment.

The sixth dimension is different, and the difference matters. It used to be "executive presence" — calm, bearing, tone, pacing, how the room felt. That was a mistake to score from a transcript: a transcript can't hear tone or see the room, so any number was really a guess dressed up as a measurement, and it was the column most corrupted by transcription noise. Jeff called this out. So the sixth dimension is now something a transcript *can* measure honestly.

### Dimension 6 — Verbal discipline (tic density)

**What it measures:** how tight the language is — the density of fillers, hedges, weakeners, and self-diminishing tics. This is a real, countable proxy for composure, and it happens to be the exact behavioral residue of Jeff's core defect: when answers are composed live instead of pre-built, they fill with "kind of," "I guess," and "or whatever." Fewer tics ≈ pre-built, confident delivery.

**Count these markers (normalize per 1,000 words so long calls don't auto-lose):**
- **Fillers** *(raw/verbatim transcript only)*: um, uh, you know, like (as filler), I mean
- **Hedges / weakeners**: kind of, sort of, I guess, maybe, probably, just, a little bit, whatever, or something, that kind of thing, I think (when it softens rather than asserts)
- **Apology / self-diminishing tics**: sorry, I don't want to bore you, I don't know, this is probably wrong
- **Self-ruleouts / rejection scripts** (e.g., "there's no way anyone knows how to spell AI over there")

**Exclude — do not count:**
- **Stutter double-renders** ("if all if all," "is there a is there a"). Speech-to-text artifacts, not disfluencies Jeff produced.
- Legitimate uses of the same words when they carry real meaning. **"just" is the biggest offender here** — it appears 13–14 times in a typical Jeff transcript and most instances are assertive ("we just have to build it"), not softening. Read each one; do not regex it blindly.

### ⚠️ Two metrics, and only one of them is scored

**Raw and cleaned transcripts are not comparable, and the published bands were calibrated on cleaned output.**

| Metric | What it is | Use |
|---|---|---|
| **Hedge density** — hedges + weakeners + apology tics + self-ruleouts, per 1,000 words | Survives every transcript type | **This is the scored metric.** Comparable across the whole pipeline. |
| **Raw filler density** — the above plus um/uh/you know/filler-like | Requires a verbatim transcript | **Logged, not scored,** until enough raw calls exist to re-derive bands. |

**Score bands — apply to HEDGE density only:**
- 5/5 — <5 — crisp, pre-built delivery
- 4/5 — 5–10
- 3/5 — 10–18
- 2/5 — 18–28
- 1/5 — >28

Applying these bands to a raw filler count drags the score down roughly two bands and produces a false flat trend. Observed raw densities (34.3 and 23–25 per 1,000) both land at 1–2/5 while the comparable hedge metric shows 3/5 and 4/5 — a real improvement that the raw number hides.

**Reporting:** cite the density band and representative markers with counts (e.g., "kind of ×3, whatever ×4, one apology tic"), so the score is auditable rather than felt. Report both numbers; score only the hedge one.

---

## STEP 1 — Pull All Scoreable Docs from Google Drive

The Meeting Notes folder ID is `[YOUR_DRIVE_FOLDER_ID]`.

```
search_files: parentId = '[YOUR_DRIVE_FOLDER_ID]' and fullText contains 'FULL RUBRIC'
```

Broaden if that returns fewer than the known count:
```
search_files: fullText contains 'SELF-EVALUATION' or title contains 'Self-Eval' or title contains 'Interview'
```

Note that not every scoreable Doc uses the literal string `SELF-EVALUATION (FULL RUBRIC)` in its heading — several use `Part 1 — This Call: Scored Rubric` instead. Search on rubric *dimension names* if a count looks short.

Read each matching Doc in full. Extract:
- **Doc title** (date + call name/company/role)
- **Call date**, **call type**, **scores for all 6 dimensions**
- **Weighted read**, **two things to sharpen**, **one thing that worked**
- **Whether the transcript was raw or cleaned** — needed to interpret Dimension 6
- **Whether the recording was complete or partial** — a partial recording is not a like-for-like data point

If a Doc has the rubric but a dimension is blank, note it as `—` and exclude it from that dimension's average.

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

RECURRING SHARPEN-POINTS
  [Pattern] — appeared in [N] of [N] calls

COACHING FOCUS — ONE THING TO PRIORITIZE
  [Highest-ROI fix. Ground it in evidence. Be blunt. One paragraph.]

WEIGHTED READ HISTORY
  [YYYY-MM-DD] [Call]: "[verdict]"
```

---

## STEP 2.5 — Push to SearchOps (interview_sessions sync)

**Added v1.3, 2026-08-12.** SearchOps (Jeff's job-search tracker, `recruiting-engine`) has an `interview_sessions` table that's a separate system of record from these Meeting Notes Docs. This step pushes each scoreable call from STEP 1 into SearchOps so the SearchOps dashboard (`/settings/interviews`, once built) reflects real interview activity instead of drifting out of sync. This is the "Google Drive Bridge" sync mechanism decided in `docs/outcome-charter-interview-sync.md` in the recruiting-engine repo — chosen over a Manual Intake form or a live MCP write-back specifically so this happens with zero new action from Jeff.

**For each scoreable Doc pulled in STEP 1** (skip ones already synced — see dedup note below), POST to:

```
POST [YOUR_APP_URL]/api/sync/interview-session
Headers:
  Content-Type: application/json
  X-Requested-With: XMLHttpRequest
  Authorization: Bearer <SearchOps sync credential>
Body:
{
  "company": "<company or org name from the Doc>",
  "date": "<call date, YYYY-MM-DD>",
  "mode": "prep" | "live" | "debrief",
  "questions_covered": ["<question text>", ...],
  "self_eval_scores": {"narrative_clarity": <1-5>, "evidence_specificity": <1-5>, "question_handling": <1-5>, "verbal_discipline": <1-5>, "fit_differentiation": <1-5>, "curiosity_questions": <1-5>},
  "notes": "<weighted read + two things to sharpen + one thing that worked, combined>",
  "transcript": "<full transcript if the Doc contains one, else omit>"
}
```

**Mode mapping:** an interview call (recruiter/HM/panel/exec) → `"live"`. Prep/mock-interview conversations → `"prep"`. Post-interview debrief-only notes with no live call → `"debrief"`. Networking-with-intent calls → `"live"` (they run the full rubric like an interview).

**Field note:** `self_eval_scores` uses `verbal_discipline` for Dimension 6, matching the v1.1 redefinition (tic density, not executive presence). Score the hedge-density metric per the bands above, not the raw filler count.

**Credential:** the `Authorization` header value is the SearchOps app password. It is **not** stored in this skill file. Jeff configures it separately in whatever secure mechanism this session has available for it (do not ask Jeff to paste it into the conversation as plain text if a more secure input path exists in this environment; if none does, treat that as a signal to fall back to graceful degradation below rather than logging the password in the transcript).

**Dedup:** the endpoint dedupes on `(company, date, mode)` server-side — it's safe to re-POST a Doc that was already synced; you'll get back the existing `session_id` instead of a duplicate row. This makes it safe to just push every scoreable Doc found in STEP 1 each time this skill runs, without tracking sync state yourself.

**Response:** `{"status": "success", "session_id": <int>}` on success. Non-200 means it failed — surface the failure in your STEP 2 scorecard output (don't silently drop it), but do not block presenting the scorecard on this succeeding.

---

## STEP 3 — Offer Next Actions

> "Want me to:
> a) Log the coaching focus to your Growth Log Apple Note so it persists across sessions?
> b) Check if any of the recurring sharpen-points appear as open Todoist tasks already?
> c) Flag the next scheduled interview in Google Calendar so I can prep you with this pattern in mind?"

Only execute what Jeff says yes to.

---

## GRACEFUL DEGRADATION

| Missing | Behavior |
|---------|---------|
| Fewer than 3 Docs | Present what exists; note scorecard gains predictive value at 3+ |
| Scores not in table format | Extract from text, flag as "manually parsed — verify" |
| Google Drive MCP unavailable | Ask Jeff to paste the eval sections; aggregate manually |
| A dimension is blank | Show as `—`; exclude from that dimension's average |
| Cleaned transcript only | Score hedge density; note fillers not measurable |
| **Partial recording** | Score it, but mark the column and say so in the trend read. Do not present it as like-for-like. |
| **A debrief already exists for the call** | Reconcile, do not re-score from scratch. The earlier doc usually had context this run lacks. |
| No HTTP-capable tool available in this session | Skip the SearchOps sync (STEP 2.5) silently for now; do not block STEP 2/3. Mention once per session: "Heads up — couldn't push to SearchOps this run, no HTTP tool available." |
| SearchOps sync credential not available in this session | Same as above — skip, note it once, don't ask Jeff to paste the password into chat. |
| SearchOps endpoint returns non-200 (server down, validation error, etc.) | Skip that Doc's sync, continue with the rest, note the failure count at the end (e.g. "2 of 5 calls synced to SearchOps; 3 failed — endpoint may be down"). |
| Doc missing a required field for SearchOps sync (company or date unclear) | Skip sync for that Doc only; it still counts toward the scorecard in STEP 2. |

---

## KNOWN DATA POINTS

*Intentionally omitted from the public copy.* The working version of this skill
carries a snapshot table of logged evals — per-dimension scores, transcript
provenance, and the names of the people on each call. That is personal interview
performance data about identifiable third parties, so it does not belong in a
public repository.

This costs the skill nothing. STEP 0 already states that Drive is the source of
truth and that a scorecard must never be generated from the snapshot — the block
is a convenience cache that has gone stale before. Running this skill against
your own Drive rebuilds the table from scratch.

## CHANGELOG

| Date | Change |
|------|--------|
| 2026-06-24 | v1.0 — created. Prereq met: 3 full-rubric evals in Drive. |
| 2026-07-15 | v1.1 — Replaced Dimension 6 "executive presence" with "Verbal discipline (tic density)". |
| 2026-08-10 | v1.2 — **Added STEP 0: Drive is the source of truth.** The v1.1 snapshot had gone three calls stale and would have produced a wrong scorecard. Added the project-folder check after a panel was scored twice by parallel sessions, producing two conflicting records. Split Dimension 6 into a scored hedge metric and a logged raw-filler metric, since applying cleaned-transcript bands to raw counts understates by ~2 bands. Refreshed KNOWN DATA POINTS to 8 calls with transcript provenance and partial-recording flags. Recorded that the four legacy asterisked scores are permanently un-derivable — no raw transcript exists before 07-16. Added "leading with the handicap" as the top recurring defect. |
| 2026-08-12 | v1.3 — **Added STEP 2.5: pushes each scoreable call to SearchOps** (`recruiting-engine`) via `POST /api/sync/interview-session`, the Google Drive Bridge sync mechanism decided in `docs/outcome-charter-interview-sync.md`. Bearer-token auth, server-side dedup on (company, date, mode). `self_eval_scores` field updated to `verbal_discipline` to match the v1.1 Dimension 6 redefinition. Graceful degradation rows added for missing HTTP tool, missing credential, non-200 response, and missing required fields. Merged forward from a local-only v1.1 draft that had branched from cloud before the v1.1/v1.2 tic-density and Drive-source-of-truth work landed — this merge reconciles both lineages onto one file. |
