# Meeting Intelligence

Event-triggered plugin that runs the full lifecycle of any call — **Prep → Rehearsal (optional) → Note Capture → Synthesis** — and ends every call with a blunt self-evaluation of how you performed.

## What it does

- **Prep** — a scannable brief before the meeting (type, agenda, questions, watch-outs). Pulls Google Calendar + prior meeting Docs + open Todoist tasks.
- **Rehearsal** *(optional)* — mock Q&A for interviews and high-stakes conversations.
- **Note capture** — paste a Granola transcript, rough notes, or type live.
- **Synthesis** — runs automatically as the tail of capture: summary, decisions, action items, open questions. No separate trigger.
- **Self-evaluation** — on **every** call. Full scored rubric for interviews (6 dimensions, evidence-quote required); a blunt 3-line read for internal calls.

## Where things go

| Output | Destination |
|--------|-------------|
| Meeting notes + self-eval | Google Docs — `Meeting Notes` Drive folder (one Doc per call) |
| Action items | Todoist |
| Growth Log, Job-Search Pipeline, Operating Manual | Apple Notes (append-style logs) |

## Trigger phrases

"Prep me for [meeting]", "rehearse [interview]", "here's my Granola transcript", "debrief [meeting]", "write up [meeting]".

## Constraints

- Granola is paste-in only (no automated pull).
- The Google Drive connector is create-only — one Doc per call; running logs stay in Apple Notes.
- After editing the skill, re-upload `SKILL.md` to Settings → Skills.

Skills: [`meeting-intelligence`](../../skills/meeting-intelligence/) (core lifecycle) and [`interview-scorecard`](../../skills/interview-scorecard/) (cross-interview trend scoring). Framework: Jeff Breunsbach meeting typology (Informational / Alignment / Decision-making).
