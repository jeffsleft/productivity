---
name: "burn-and-rebuild"
description: "Jeff's ideation flow: clarify a challenge, interview him, give top-10 and bottom-10 ideas, discuss, then on his command discard them and rebuild 8 ideas from first principles."
---

# Burn and Rebuild

Trigger on: "/burn-and-rebuild", "burn and rebuild", "run burn and rebuild on X", "top 10 and bottom 10", "help me think better about X", or when Jeff brings a challenge, question, or research prompt and asks for this process.

## Why this skill exists

An LLM's first answers are the consensus: a blend of everyone's thinking, smart and careless alike. This skill uses that on purpose. Phase 4 gets the consensus out in the open: the best of it (top 10) and the tempting traps (bottom 10). Phase 6 then throws all of it away and rebuilds.

**Throwing out the list does not make the rebuild original.** Phase 6 runs on the same model as Phase 4, so without constraints it will reword the discarded ideas. Only two things make it different:
1. **Jeff's context from the interview**: his constraints, what he has that others don't, what he has already tried, and what he knows that isn't public. This is the non-consensus input. Treat the interview as the core of the process, not a formality.
2. **The Phase 6 rules**: start from core facts, trace 2nd- and 3rd-order effects, and check every new idea against all 20 discarded ones.

## Hard rules (apply to every phase)

- **Gates are hard.** Never move to the next phase without Jeff's go-ahead. Never start Phase 6 until Jeff explicitly tells you to eliminate the lists (e.g. "burn it", "eliminate all those", "start from scratch", "rebuild").
- **Label epistemics.** Put [Inference], [Speculation] or [Unverified] at the start of any claim that isn't sourced. Apply [Unverified] to prevent / guarantee / will never / fixes / eliminates / ensures unless sourced. Cite sources for researched facts.
- **State judgment calls in one line.**
- **Direct and blunt.** No compliments, no recaps of what Jeff said. Where there's evidence, disagree.
- **Sub-skills may write only to Apple Notes.** No Todoist edits, no CONTEXT.md / HANDOFF.md / ADR writes, no memory writes during this flow. Reading anything is fine.
- **No gaps filled with assumptions.** If something needed is missing, ask.

---

## Phase 1: Intake

Jeff proposes a challenge, question, or research prompt. Check what kind it is:
- **Decision** (choose between paths)
- **Problem** (something is broken or stuck)
- **Opportunity** (how to win or grow)
- **Research** (what is true about X)

If it is unclear, ask which one. Research prompts set a higher evidence bar in Phase 4.

## Phase 2: Clarify (read before asking)

Before asking Jeff anything, read what is already known:
1. The current conversation.
2. User memory (`memory_list`, then read the relevant files) and the attached Project docs (`project_search` / `project_read`).
3. Connected project folders: CLAUDE.md, CONTEXT.md, HANDOFF.md, dated notes.

Then post a **Draft Problem Frame** in chat:
- **The question**: one sentence, sharpened.
- **The decision it serves**: what Jeff will do differently depending on the answer.
- **What "solved" looks like**: observable success criteria.
- **Known constraints**: time, money, people, non-negotiables.
- **Already tried or ruled out.**
- **Gaps**: what you don't know yet. These become the interview's agenda.

Asking a question whose answer was already knowable is a failure.

## Phase 3: Interview (grill live, then Walk & Talk)

**3a. Grill live.** Invoke `grill-with-docs` (fall back to `grill-me` if no project docs exist) in interactive mode, with these limits:
- Scope: lock the problem definition only (the frame from Phase 2). Aim for 5–10 questions, each with a recommended answer.
- Record resolved terms and decisions **in chat**. Do NOT write CONTEXT.md, ADRs, or HANDOFF.md.

**3b. Walk & Talk list.** Invoke `chief-of-staff:walk-and-talk-interviewer` in pre-walk mode, with these limits:
- Scope: only what is still unclear after 3a. **Never re-ask anything 3a resolved.**
- Run its Steps 2–4 only: read, pick topics, build the script. Write the script to Apple Notes (its standard folder and HTML formatting) and post it in chat.
- The question list must get at what gives Jeff an edge. Always include questions on:
  - what Jeff has that others attempting this don't (skills, relationships, position, data, timing)
  - what he believes that most people in this space would disagree with
  - what he has seen fail up close
  - which outcome he would regret more: acting and failing, or not acting
  - who wins or loses if this succeeds
- When the transcript comes back, pull the answers into this flow. **Skip Walk & Talk Steps 5–6** (no Todoist write-back, no report file, no Organizer handoff).

**Gate: Problem Brief.** Post the final brief: the Phase 2 fields updated, plus **Jeff's unfair advantages** and **the beliefs he holds that most people don't**. Get Jeff's confirmation before Phase 4.

## Phase 4: Top 10 / Bottom 10

If the prompt needs facts (always for Research prompts), do web research first. Cite sources.

**Top 10: the best of the consensus.** Scoring: Impact × Feasibility × Evidence strength, each scored 1–5. State the scoring in one line; Jeff can override it. Table columns:

| # | Idea | Why it's strong | I/F/E | Evidence / source | Biggest risk |

**Bottom 10: relevant but full of holes.** These must be ideas a smart person would plausibly propose, the tempting traps. No strawmen or joke ideas. Table columns:

| # | Idea | Why it's tempting | The holes (specific) | What it gets wrong about the problem |

Close with a short **Pattern read**: what the 20 have in common, i.e. which assumptions the consensus leans on. This feeds Phase 6.

## Phase 5: Discussion

Jeff leads. Answer directly, defend or drop ideas based on evidence, and push back where it's warranted.

Keep a **Lessons Ledger** in chat: constraints, insights, and "never again" rules that come out of the discussion (e.g. "anything that needs a sales team fails; Jeff has no sales capacity"). Ideas don't go in the ledger; lessons do. Show the ledger when Jeff signals the discussion is winding down.

**Gate:** wait for Jeff's explicit elimination command.

## Phase 6: Burn and Rebuild (8 ideas)

On Jeff's command:

**What carries forward:** the Problem Brief and the Lessons Ledger.
**What gets discarded:** all 20 ideas. They are kept only as a **Do-Not-Repeat list** for the novelty check.

**Step 1: Core facts.** List the facts about the problem that can't be broken down further: economics, human behavior, physical and time limits, Jeff's actual assets. Separately, list the **conventions**, the assumptions the consensus treated as facts (use the Phase 4 Pattern read). Label each one fact or convention, and give the source or basis for each.

**Step 2: Diverge on purpose.** Build a pool of about 20 candidates using these lenses. Use at least 5 lenses, and no more than 3 finalists from any single lens:
- **Invert a convention**: what if the assumed-true thing is false?
- **Unfair advantage**: what only Jeff can do, given the Brief.
- **Subtraction**: solve it by removing something, not adding.
- **Change the actor**: a different customer, partner, or owner of the problem.
- **Change timing or sequence**: do it earlier, later, or in a different order.
- **Distant-domain analogy**: how a structurally similar problem was solved in an unrelated field. Name the field and the actual case.
- **Make the constraint the product**: turn the binding constraint into the advantage.

**Step 3: Effect chains.** For each candidate: 1st-order effect → 2nd-order → 3rd-order. Include at least one negative or unintended effect per chain. Drop any candidate whose 3rd-order effects undermine the goal.

**Step 4: Novelty check.** For each survivor, name the closest idea on the Do-Not-Repeat list and state the *structural* difference (a different mechanism, actor, or assumption; not different wording). If the difference can't be stated in one sentence, cut it.

**Step 5: Choose 8.** For each idea:
- **Idea**: one sentence
- **Core fact it rests on**
- **Convention it breaks**, if any
- **Mechanism**: why it works
- **Effect chain**: 1st → 2nd → 3rd order
- **What must be true**: the assumptions to test
- **Cheapest test**: the smallest action that proves or kills it within 1–2 weeks
- **Novelty note**: closest discarded idea and how this differs
- **Confidence**: High / Med / Low, with a label ([Inference] / [Speculation])

Deliver all 8, ranked by confidence. If some are weak, say so plainly. Don't pad them to look strong.

## Phase 7: Discussion and Next Steps

Discuss the 8 with Jeff. Close with **actionable next steps**: which cheapest tests to run, who runs them, and by when. Label anything that can't be acted on as [Directional].

## Session record (.docx)

When Jeff closes the session (or asks for the record), load `document-system`, then `docx`. Produce one file named `YYYY-MM-DD-burn-and-rebuild-<topic-slug>.docx` containing:
1. SCR summary (Situation, Complication, Resolution)
2. Problem Brief
3. Top 10 and Bottom 10 tables, plus the Pattern read
4. Lessons Ledger
5. Core facts vs conventions
6. The final 8 (full format)
7. Next steps
8. Flags: everything that requires internet (links, sources), with an offline-safe note

Save it to the relevant connected project folder. If more than one is connected, ask which. Tell Jeff the folder and file name.

## Failure modes to watch

- The rebuild rewords top-10 ideas. The novelty check exists to catch this, so use it.
- The bottom 10 are strawmen. They must be plausible traps.
- Asking something already in memory, project docs, or earlier in the chat.
- Duplicate questions between the grill and Walk & Talk.
- Moving past a gate without Jeff's go-ahead.
- Sub-skills writing to Todoist or CONTEXT.md during this flow.