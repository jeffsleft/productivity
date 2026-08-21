# Chief of Staff — Claude Skill Set

A set of six Claude skills that together do the job of a chief of staff: hold the whole
picture of your work so you don't have to carry it in your head.

Built and run in production by one person since May 2026. This README is written to be
read either by a human or by an AI agent evaluating whether the pattern is worth copying.

---

## The problem

Most people use Claude as a very good conversationalist. Open a chat, explain your
situation from scratch, get something useful, close the tab. The context evaporates.
Next week you explain it all again.

That's fine for one-off questions. It fails at the thing most of us actually need help
with: the ongoing, never-finished work of staying on top of your own commitments. Task
lists rot. Decisions get made and then quietly abandoned. You leave a meeting with three
commitments and honor one.

A chief of staff is the human answer to that. This is an attempt to build it as software.

---

## First — what a "skill" is

A skill is a written instruction sheet you save once, and Claude loads automatically when
the moment calls for it.

That's the whole idea. It's a markdown file with two parts:

1. **When to use it** — "run this when the user says *organize my tasks*"
2. **The instructions** — the steps, the rules, the format of the output

You aren't programming. You're writing down how you want a job done, the way you'd brief
a new assistant. The difference from pasting a prompt each time is that Claude decides on
its own when it applies, and the instructions stay consistent instead of drifting with
your mood.

A **plugin** is a bundle of related skills shipped together. This is one.

---

## The six skills

One orchestrator, four workers, one standing check. The orchestrator runs the others in
order and passes each one's output into the next.

| Skill | Role |
|---|---|
| [`cos-sync`](../../skills/cos-sync/) | Orchestrator — runs the full session in sequence |
| [`walk-and-talk-interviewer`](../../skills/walk-and-talk-interviewer/) | Phase 1 — extract context from your head |
| [`todoist-organizer`](../../skills/todoist-organizer/) | Phase 2 — audit and prioritize |
| [`execution-shadow`](../../skills/execution-shadow/) | Phase 3 — do the work |
| [`decision-accountability`](../../skills/decision-accountability/) | Standing — catch abandoned decisions |
| [`weekly-review`](../../skills/weekly-review/) | Weekly strategic pass (runs standalone) |

### Phase 0 — Project Health Check

Before anything else, `cos-sync` reads a spreadsheet listing every active project, its
status, current milestone, and a health color. It prints that snapshot.

Why first: it stops the session from collapsing into a task-list session. You see the
altitude before you see the weeds. Read-only — it never edits the sheet.

### Phase 1 — Walk & Talk Interviewer

The least obvious skill and the most useful one.

It scans your task list for tasks that are vague, stalled, or have been sitting for weeks
— the ones that read like `Follow up with Dave` and mean nothing three weeks later. Then
it generates interview questions about them.

You go for a walk. You answer out loud into a voice memo. You come back and paste the
transcript.

Claude takes what you said and **writes it back into the task manager** as real
descriptions, subtasks, and next actions. `Follow up with Dave` becomes `Email Dave re:
whether the Q3 budget line survived — he said he'd know by the 12th`.

The insight: tasks don't stall because you're lazy. They stall because they were never
written clearly enough to start. The context is in your head, and it's easier to say it
than type it.

### Phase 2 — Todoist Organizer

An audit pass over the whole list. Closes dead tasks, merges duplicates, assigns
priorities, proposes dates for undated work, and tags each task with **who should do it**
— Claude alone (🤖), Claude with you (🤝), or you alone (👤).

That last tag is the part worth stealing. Tagging by *AI leverage* turns a task list into
a work queue. Anything marked 🤖 is something you never have to think about again — it
just needs a session.

Proposes everything, then asks once before writing. Nothing changes silently.

### Phase 3 — Execution Shadow

Takes the 🤖 queue from Phase 2 and actually does the work. Drafts the proposal, runs the
research, writes the document. Not a plan to do it later — the output itself, in session.

This is the payoff. Phases 0–2 exist to produce a queue clean enough that Phase 3 can run
without supervision.

### Standing — Decision Accountability

Reads a running decision log and surfaces decisions that are overdue for follow-up,
contradicted by tasks still sitting open, or silently abandoned.

Small skill, disproportionate value. Most things don't fail because you decided wrong.
They fail because you decided and then nothing happened.

### Weekly — Weekly Review

A separate Sunday pass that asks the strategic question the daily session can't: *am I
working on the right things at all?* Combines an altitude check against your project
sheet with a full task audit and a load plan for the week.

---

## Why the sequence matters more than any single skill

Each phase exists to make the next one possible.

> **Health check** → which projects deserve attention
> **Interview** → turns fuzzy tasks into clear ones
> **Organizer** → sorts clear tasks into a prioritized queue
> **Execution** → works the queue

Run Execution first and it works on garbage. Run Organizer without the Interview and it
carefully prioritizes tasks nobody understands.

This generalizes well past chief-of-staff work. Almost any recurring process worth
automating has this shape: **gather context → clarify it → prioritize it → act on it.**
Most people build the "act on it" part first and wonder why the output is thin.

---

## What it requires

**Connectors (MCP).** These skills only work because they read and write real data:

| Connector | Used for | Required? |
|---|---|---|
| Todoist | the task list itself | **Yes** — the whole thing is built on it |
| Apple Notes | walk transcripts, decision log | Yes for Phases 1 and Decision Accountability |
| Google Drive | the project health spreadsheet | Optional — Phase 0 skips silently without it |
| Calendar | scheduling around fixed blocks | Optional |

**A task manager you actually maintain.** If your list is a graveyard, these skills
organize a graveyard. This is the single biggest predictor of whether it works.

**A way to record voice.** Any voice memo app. The walk is the point.

---

## Is this for you?

**Good fit if:**

- You already keep a task manager and it's roughly current
- Your work is project-shaped — several parallel threads, each with its own state
- You lose things in the gap between "decided" and "done"
- You're willing to spend an afternoon on setup for a return that shows up around run five

**Poor fit if:**

- You don't have a task manager, or you have one you ignore
- Your work is mostly reactive and same-shaped day to day (a queue, a shift, a caseload)
- You want something that runs unattended — this asks for confirmation constantly, by design
- You're looking for a productivity system. This isn't one. It's an assistant *for* the
  system you already have.

**Honest caveats:**

- It doesn't decide for you. It surfaces, proposes, and drafts. Judgment stays yours,
  which is correct, but it means it can't rescue you from avoidance.
- Setup is front-loaded — connecting tools and adapting the first skill is real work.
- The skills are opinionated about priorities and cadence. You'll be editing them.

---

## Adapting it

The skills ship with placeholders where personal details go:

| Placeholder | Replace with |
|---|---|
| `[YOUR_TODOIST_UID]` | your Todoist user ID |
| `[PARTNER_TODOIST_UID]` | a collaborator's ID, if you share projects |
| `[PARTNER]` | the person you share a household or projects with |
| `[YOUR_SHEET_ID]` | your project-health spreadsheet ID |
| `[YOUR_DRIVE_FOLDER_ID]` | where session reports get filed |
| `[YOUR_PROJECTS_PATH]` | your local projects directory |

Also worth rewriting for yourself: the **priority weighting** (each skill has a line like
`Priority weight | A → B → C`) and the **project trees** the organizer walks. Those encode
one person's life and are the first thing that will feel wrong.

**Install:** copy any skill directory into `~/.claude/skills/` and restart Claude Code.
Skills must be a directory containing `SKILL.md` — a flat `.md` file loads in some
contexts and silently fails in others.

```bash
cp -r skills/cos-sync ~/.claude/skills/
```

---

## If you want to build your own instead

Don't start with six skills. Start with one — the thing you already do manually and resent.

1. **Write down how you'd brief an assistant to do it.** Plain language, the steps, the
   rules, the output format you want. That document *is* the skill.
2. **Connect one real tool.** Whatever holds the actual data. The skill gets ten times
   more useful the moment it can read something true.
3. **Make it propose, not execute.** Show what it plans to change and wait. This is about
   trust more than safety — you'll stop running anything that surprises you once.
4. **Run it five times and edit the instructions each time.** The first version is always
   wrong in ways you can't predict. Skills get good through use, not design.
5. **Only then add a second** — and only if the first keeps handing you the same
   unfinished job.

The failure mode is building an elaborate system on day one and running it twice. What
compounds is a small skill you run every week for six months.

---

## Design principles worth stealing

- **Ask before writing.** Every phase proposes and waits.
- **Pausable.** Stop after any phase, resume later. Sessions demanding 90 uninterrupted
  minutes don't get run.
- **Leaves a record.** Each session writes a dated report, which is what makes the *next*
  session cheap — it starts from the last one instead of from zero.
- **Sub-skills stay independently callable.** The orchestrator sequences them, but any one
  can run alone. Bundles that only work as a bundle don't survive contact with a real week.

---

*Part of [jeffsleft/productivity](https://github.com/jeffsleft/productivity). Skills are
written for Claude Code and Claude Cowork.*
