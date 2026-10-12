---
name: harvest-intel
description: Extracts substantive learnings from the current conversation — new facts, corrections to prior assumptions, insights about whatever subject is being tracked (a company you're interviewing with, a purchase you're researching, any project) — and merges them into that project's living reference doc(s), with conflict detection so nothing gets silently overwritten. Use whenever the user asks to "save what we learned," "harvest this," "fold this into the doc," "update the [project] file with what we discussed," "capture this before we lose it," or wants a conversation's findings written into a persistent file instead of left to evaporate. Trigger only on explicit request — never automatically — since captured content is often sensitive (deal terms, personal research). Pairs well as a follow-up to grill-with-docs or define-done, but stands alone and works on any project, not tied to a specific file name or structure.
---

# Harvest Intel

## What this does and why

Conversations produce real findings — a fact gets confirmed, an assumption gets corrected, a new angle emerges — and then the chat ends and that thinking either lives in someone's memory or it doesn't. This skill closes that gap: it reads back through the conversation, pulls out what's actually new and worth keeping, and writes it into the project's own reference document(s) — not a generic summary, but a proper merge into the file(s) the user already treats as their source of truth for that project.

It's deliberately not tied to any one file name, folder structure, or subject. The same skill should work whether the "project" is a job-search war room with four interlocking files, a single running doc on whether to install solar panels, or notes on a different company's interview process. What stays constant across all of them is the *behavior*: find the right file, don't lose or contradict what's already there, and don't make the user reconstruct your reasoning to trust the result.

## Step 0 — Confirm you know where things go, and don't guess if you don't

Before touching anything, work out which file(s) in this project are the living reference doc(s) — the place new facts about the subject belong.

1. Look for a `CLAUDE.md` in the project folder. If one exists and describes the doc structure (which file holds what), use it — this is the strongest signal, because it means a prior session already worked this out deliberately.
2. If no `CLAUDE.md` exists, or the project has multiple candidate files and it's genuinely unclear which one this content belongs in, **ask the user once** rather than guessing. Offer to write a minimal `CLAUDE.md` afterward so this doesn't need re-asking next time.
3. If the conversation contains unresolved ambiguity or an open question that needs the user's judgment before anything can be written correctly (for example: two conflicting facts and no way to tell which is right from the conversation alone), don't try to resolve it yourself. Hand off to whichever tool fits — `grill-with-docs` or `grill-me` to interrogate the specifics, or `define-done` if the scope of what's even being captured is unclear — and come back to harvesting once that's resolved.

Skipping this step is the single most common way this goes wrong: writing confidently to the wrong file, or inventing a new file the user didn't want, is worse than pausing to ask.

## Step 1 — Scope the conversation

By default, read the whole conversation from the start, not just the last few messages. The user is invoking this deliberately at a point they consider a natural stopping point, so the full session is fair game unless they say otherwise (e.g., "just from where I said 'starting fresh'").

## Step 2 — Filter: subject-matter, not meta-conversation

A single session usually contains more than one kind of content. Separate:

- **Subject-matter learnings** — new facts about the thing being tracked, corrections to previous understanding, insights, decisions about the subject itself. This is what gets harvested.
- **Meta-conversation** — discussion about tools, process, how documents are organized, what skill to use, requests to Claude about *how to work*. This does not belong in the project's reference doc and should be left out, even if it happened in the same conversation.

If the session was, say, half about restructuring how a project's files are organized and half about new facts learned about the subject, only the second half is harvest material. Don't write "we reorganized the docs today" into a company research file — that's true, but it's not what the file is for.

## Step 3 — Tag confidence, lightweight by default

Apply the user's standing epistemic-integrity habits: label anything inferred, speculated, or not independently confirmed — `[Unverified]`, `[Inference]`, `[Speculation]` — rather than presenting it as settled fact. A short source tag (where this came from — "chat 2026-08-01," "call with X," a URL) and it's enough.

Check whether the project already runs a heavier verification system — for example a dedicated tracker file with status tags (look for something like `*Verification-Tracker*.md` or a similar convention described in the project's `CLAUDE.md`). If one exists, route new claims through it the way the project already does. If it doesn't exist, don't invent one — the lightweight inline tagging is the default, not a fallback. Most projects don't need a full verification pipeline; match the rigor to what's already there instead of imposing a heavier standard than the user asked for.

## Step 4 — Merge, don't regenerate

The default behavior is a surgical addition: find the right existing section(s) in the target file(s) and add the new material there, in the voice and format the document already uses. Don't rewrite the whole file from scratch — that's slower, riskier, and throws away the chance for the user to see exactly what changed.

Only do a full-file restructure if the user explicitly asks for one, or if it's genuinely impossible to merge cleanly (for example, the new information invalidates the file's entire existing structure, not just one section — this should be rare and worth flagging as unusual before proceeding).

## Step 5 — Detect conflicts before writing — never silently overwrite

Before adding anything, check whether it contradicts what's already in the target file. This is the most important behavior in this skill: reference docs like these often carry irreplaceable accumulated context — quotes, confirmed facts, prior decisions — and silently overwriting or contradicting that content without flagging it is the worst failure mode available here, worse than being slow or asking too many questions.

If new information conflicts with existing content:
1. Don't edit yet.
2. Show the user both sides plainly — what the document currently says, and what the new information suggests.
3. Ask which is correct (or whether both are true in different contexts, which happens more often than it seems).
4. Only write once that's resolved.

If there's no conflict, this step is quick — most new information will simply extend or corroborate what's there.

## Step 6 — Preview before writing

Before making any edit, show the user what's about to be added or changed — which file, which section, and the actual text. This isn't a rubber-stamp formality; it's the point where the user catches something you misread from the conversation before it becomes a permanent part of a document they're relying on. Once they confirm, write it.

## Step 7 — Match the document's existing conventions

Look at how the target file already handles things like a maintenance log or changelog. If it already appends dated entries at the bottom describing what changed, add one, in the same style. If it doesn't have that convention, don't introduce one uninvited — match the document as it already is rather than imposing a new structure on it.

The same goes for tone, table formats, heading levels, and citation style — the goal is that a new entry reads as if it always belonged there, not like it was pasted in from somewhere else.

## Portability note

This skill has no hardcoded file names or folder assumptions. It operates on whatever project is currently active in the conversation — the folder the user has open, the files they've been discussing. The mechanism that makes it reusable across completely different projects (a job search, a home-purchase research doc, a different job search for a different company) is Step 0: always resolve the target file(s) from the project's own `CLAUDE.md` or by asking, never by assuming a specific file name exists.

## Example

**Input (end of a conversation):** the user has spent a session confirming a fact they'd previously assumed was true turned out to be wrong, learned three new details about a subject they're tracking, and also spent part of the conversation deciding how to reorganize their notes folder.

**What gets harvested:** the corrected fact (flagged as a conflict, resolved with the user first) and the three new details, each tagged with today's date and a short source note, merged into the correct existing sections of the project's reference file. The folder-reorganization discussion is left out — it's about the notes system, not the subject the notes are about.
