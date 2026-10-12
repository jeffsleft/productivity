---
name: "internal-writing"
description: "Apply Jeff Beaumont's internal writing standards for strategy documents, proposals, leadership emails, and weekly snapshots. Use this skill whenever Jeff is writing for an internal audience with authority to act: CS Ops strategy docs, VSM/VSA frameworks, project briefs, investment proposals, leadership emails, his Friday weekly snapshot, or a strategy document sent to a company that has not hired him yet. Triggers include: \"help me write a snapshot,\" \"draft a strategy doc,\" \"write a proposal,\" \"draft an email to leadership,\" \"weekly update,\" \"help me write to my manager,\" \"write a role definition,\" or any request for internal business communication."
---

# Jeff Beaumont · Internal Writing Skill
### Version 1.3 — August 2026

## What internal writing is

Internal writing is produced for audiences with authority to act: leadership, peers, direct reports, and founders considering hiring him. The voice is the same as public writing: direct, grounded, human-first. The structure is more explicit and the sequencing more disciplined.

**Two hard rules for every format:**
1. Always end with explicit next steps and named owners. If an action has no owner, it does not belong.
2. "We" over "I" for what a team accomplished. See the I/we rule below for the exception that matters.

**Pair this with the `voice-engine` skill** for sentence-level voice. This skill governs structure and sequencing; voice-engine governs how the sentences sound and what gets cut.

---

## Em-dashes

Target zero per document. Accept one or two only when no good alternative exists. Default to periods, commas, colons, and semicolons. Em-dashes are an AI writing signal. Flag every one in review.

---

## Format 1: Strategy and Reformulation Documents

Used for CS Ops strategy, Digital Strategy, Renewal Ops, VSM/VSA frameworks, role definitions, and any document diagnosing an area of the business and prescribing direction.

**Section order. Diagnosis comes first.**

| Section | What goes here |
|---|---|
| **Charter** *(optional)* | One or two sentences. What does this function exist to do? Purpose, not tasks. Skip it when the document is about a problem rather than a standing function. |
| **Diagnosis** | Named problems as declarative sentences. Specific problems, not areas of concern. Bold the problem name, then one or two sentences of substance. |
| **Beliefs** | Declarative assumptions bridging diagnosis and guiding policy. Only the contested or non-obvious ones. Bold the belief name, one sentence each. |
| **Guiding Policy** | The overall approach in prose, then named principles. Answers "how do we operate?" not "what do we do?" |
| **Coherent Set of Actions** | Explicitly labeled. Each action traces back to a specific diagnosis item. If you cannot draw the line, the action does not belong. |
| **Success Criteria** | Observable outcomes, measurable where possible. |

**On the ordering.** Earlier versions of this skill put Beliefs and Guiding Principles ahead of Diagnosis. That was wrong. Jeff's own convention, stated in his leadership deck and used in both surviving GitLab strategy documents, is Diagnosis first: *"I write strategy in four sections — Diagnosis, Beliefs, Guiding Principles, and a Coherent Set of Actions — and I keep diagnosis and actions separate so it's always clear whether the actions actually address the problem."*

**Attribution note.** The Beliefs section is Dave Kellogg's addition to Rumelt's kernel, not Jeff's invention. He credits it in his own writing. Do not imply otherwise.

**The test he applies:** if you removed the diagnosis, would the actions still make sense? If yes, it is a to-do list, not a strategy.

**Belief sentence examples:**

Good: "Clarity is key; clarity of roles, expectations, mission, vision, and direction."
Bad: "We should work to ensure everyone has a clear understanding of their areas."

Good: "CS Ops must be proactive vs. reactive."
Bad: "We aim to be more strategic in our approach over time."

Good: "It is difficult to create and maintain genuinely simple processes."
Bad: "Process design requires careful consideration of multiple stakeholder needs."

**Guiding principle examples**, in his named-and-colon format:

- "People First, Results Second: People first and results will follow."
- "Prioritize vs. boil the ocean: We must be selective with how we spend our time and resources."
- "Strategic vs. tactical: CS Ops will deliver more value starting with strategy (diagnosing the problem) and later applying tactics."
- "Long term direction: We must move from short-term thinking to long-term scoping and planning."
- "Deep, not wide: Make the difficult tradeoffs rather than attempting to satisfy all stakeholders."

**A named four-part operating sequence he uses for operations programs:** rigor, then visibility, then accountability, then iteration. The order is the argument — you cannot measure what is not defined, hold anyone to a number that is not measured, or improve what nobody reviews. Rigor and visibility are the build, accountability is the operating rhythm, and iteration is what keeps it from drifting back.

**Length.** A working strategy document runs 1,500 to 2,500 words. Anything past 3,000 gets cut. The framework structure already signals rigor; volume does not add to it. When Claude drafted a 4,800-word role definition, Jeff cut it to 1,700.

---

## Format 1b: Writing to people who have not hired you yet

A strategy document sent to a company evaluating him is not an internal document, even though it uses the internal format. The structure holds. Four things change.

**State problems at class level, not instance level.** *"There is no agreed set of operating measures"* becomes *"a growing company can struggle to maintain clear, consistent processes."* He earns the right to be specific about their company after he is inside it. Before that, a specific accusation invites the reader to defend rather than think.

**Concede in the same breath as the indictment.** *"Client delivery has no single owner. The VP Operations seat is not being backfilled and managers run their own areas. Fine for a small company, gets harder at scale."* The concession is not softening. It removes the reader's need to argue so the problem can be used.

**Leave out anything that presumes the job.** No negotiation terms, no working-hours position, no compensation questions, no first-90-days plan. Jeff drafted all four into a role-definition straw man and cut all four before sending. A 90-day plan for a job nobody has offered reads as presumption, and it converts a document about their problem into a document about his terms.

**Do not include anything you only know because one insider told you.** Details sourced from a single sympathetic contact expose that contact when the document reaches the whole leadership team, and they raise the question of what else that person has shared. Keep what any thoughtful outsider could reason to.

---

## Format 2: Project Briefs and Proposals

Used for investment proposals, process changes, headcount requests, project approvals. Goal: make the decision easy.

**SCR Template:**

| Section | What goes here |
|---|---|
| **Situation** | Current state, specific. Name the metric, process, gap. Do not assume shared context. |
| **Complication** | Why it is a problem. Name the cost in time, risk, money, or mission impact. Urgency lives here. |
| **Resolution** | What to do and what it delivers. Direct about the ask. Anticipate the objection. |
| **Next Steps** | Who does what by when. Named owners. |

**Opening rules:**
- Open with the situation in one sentence. "Cash approval requests currently pass through four roles and average 2.3 days."
- State the ask explicitly and early.
- Include a mission link: one sentence tracing the benefit to the people affected. Not inspirational language, the honest reason it matters.

Example: "Reducing the cash approval cycle from 2.3 days to 1 day returns approximately 3 hours per week of crew administrative burden back to the wards."

---

## Format 3: Leadership Emails

- State the ask in the first sentence or subject line. Not buried, not warmed up to.
- One email, one ask. Multiple unrelated asks means multiple emails, or numbered items with explicit labels.
- Lead with conclusion, then context. Context-before-conclusion buries the ask.
- **Use numbered lists for anything enumerable.** Two prose paragraphs describing two attachments became `1.` and `2.`, one sentence each.
- **Do not describe the value of what you attached.** Say what it is. "The second is a job description for you to tear apart and shred" beats "a job description you could actually post, ready to go if you want to run a real search." The first invites; the second sells.
- **Explain an ask by disclosing how you work, not by instructing the reader.** *"For me, it's a lot easier to critique something than creating from nothing, so having these docs could help us really think through what it is we're trying to do."*
- Signature block on anything external: name with credentials, email, cell.
- Target 120 to 180 words for a substantive email with attachments.

Good: "Asking for approval to proceed with [X]. Here is the situation and what we are recommending."
Bad: "Hope you are doing well. I wanted to reach out about something we have been working on..."

---

## Format 4: Weekly / Bi-Weekly Snapshot

A curated view of what matters, filtered through leadership's priorities. Not a status report.

**Template (plain text body, no markdown bold or headers in the email itself):**

```
Happy Friday.

Word count: [X] ([Y] minutes)

One big thing: [one sentence — names a tension, risk, or theme. Takes a position.]

Highlights
1. [Full sentence. Named people get credit.]
   * [sub-detail if needed]
2. [Full sentence.]

Insights
1. [What the data or events mean. Not more highlights.]
2. [Analysis. What leadership should pay attention to.]

Blockers / Watchpoint
1. (Blocker) [item — status and next step]
1. (Watchpoint) [item — what to watch and why]

Next
1. [Item for next week]
2. [Item for next week]

Thought for the day: [optional — dry, human, brief. Skip explicitly if the week is too serious.]
```

**Formatting rules:**
- Headers are plain text labels, not bold or markdown
- Items numbered (1. 2. 3.), not bulleted with dashes
- Sub-details use `*` on the next line, indented under the parent
- Inline checkmark for completed items
- No em-dashes in headers or item labels

**Word count target:** 150–300 words. State word count and read time at the top.

**One Big Thing rules:**
- NOT the top highlight. The single sentence leadership most needs to understand this week.
- Names a tension, risk, or theme. Takes a position. Not neutral.
- Examples: "Month-end is clean, but the treasury chain is exposing a control gap we have not named yet." / "The FD bottleneck is not a process problem. It is a design problem."

**Section distinctions:**
- Highlights: what happened. Named people get credit. Full sentences.
- Insights: what it means. Analysis. Not more highlights.
- Blocker = actively preventing progress. Watchpoint = risk that may become one. Label each.

**Acknowledging misses:** state directly, without qualification. "Will miss Q3 OKR because of Q3 GTP and other priorities." One sentence. Move on.

**Thought for the day:** dry, human, brief. Skip explicitly when the week is too serious.

---

## I/we rule

- **"I"** for personal perspective, argument, and accountability for a decision.
- **"we"** for what a team did together. This is the default and it holds in most writing.
- Never "we" when taking a personal position. That diffuses accountability.
- Never "I" for something the team accomplished. That erases credit.

**The exception that matters, and it is protective rather than possessive.**

When "we" would attribute a position to someone who is not in the room, or to someone who would absorb the cost if the audience rejects it, switch to "I" and carry it alone.

Jeff, on why he changed *"we landed on an order"* to *"I proposed an order of operations"* in an email to three cofounders: **"I did not want to speak on the founder's behalf to his cofounders in case they disagreed. I would rather they disagree with me than him."**

**The test: if the reader disagrees with this sentence, who pays for it?** If the answer is anyone other than Jeff, use "I."

This fires most often when writing to a group that contains a sponsor, an ally, or anyone whose standing could be spent on his behalf. It is not about claiming credit. Reading it that way produces the wrong instinct and the wrong sentence.

---

## Anti-patterns for internal writing

| Pattern | Why it fails | Fix |
|---|---|---|
| Selling the document inside the document | Commentary on a document's own quality undercuts it; the artifact is the argument | Say what something is, never why it is good |
| Tactics disguised as strategy | Lists next steps with no diagnosis | Write diagnosis before actions |
| Executing before diagnosing | Addresses symptoms | Diagnosis section first |
| Actions with no named owners | Nothing will happen | Every action has a named owner and a date |
| Boiling the ocean | No decisions have been made | Name the two or three highest-leverage actions |
| Context before conclusion in emails | Buries the ask | Lead with conclusion |
| "We" that puts a position in an absent person's mouth | If the room disagrees, they absorb it instead of Jeff | "I" whenever someone else would pay for the pushback |
| Defending length instead of admitting it | Three sentences of justification where one clause would do | "It makes it a bit long, but..." then move on |
| Polishing into epigram | Symmetry at the cost of accuracy | "v1 is not the constraint. V2 through v10 is." beats the neater version |
| The "we"-less snapshot | Erases team effort | "We" for shared work |
| Blockers with no status or next step | Alarm without resolution | Status, owner, timeline for each |
| Insights that are just more highlights | Describes what happened, not what it means | Insights answers: what does this mean? |
| Em-dashes connecting clauses | AI signal | Period or semicolon |

---

## What to give Claude by format

| Format | What Claude needs |
|---|---|
| Strategy doc | Charter in one sentence, 3–6 diagnosis items, contested beliefs, 2–3 confirmed actions, and whether the audience has hired him yet |
| Proposal | Situation in one sentence, complication (what it costs and to whom), the ask, mission link |
| Leadership email | The ask, minimum context, any objection being preemptively addressed, and who else is on the thread whose standing could be spent |
| Weekly snapshot | One Big Thing first, then highlights, insights, blockers. Word count target. Audience names and titles. |

---

## Pre-send checklist

- Diagnosis in its own section, before beliefs and actions?
- Every action traceable to a specific diagnosis item?
- Every action has a named owner and a date?
- Ask stated explicitly and early?
- Mission link included where relevant?
- Any "we" that would make someone else absorb the disagreement? Change it to "I."
- Any sentence that explains why the document is good? Cut it.
- For external audiences: any claim sourced from one insider? Any section that presumes the job?
- Word count within range for the format?
- Em-dashes at zero?
- Anything softened that should be stated directly?

