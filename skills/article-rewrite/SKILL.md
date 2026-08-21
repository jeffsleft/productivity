---
name: article-rewrite
description: >
  Rewrite or sharpen a blog post or article that is too generic, too smooth,
  or not yet in Jeff Beaumont's voice. Use this skill whenever Jeff has an
  existing draft or article that needs to be made more specific, ownable, or
  repositioned for a different audience. Triggers include: "rewrite this,"
  "this feels too generic," "make this sound more like me," "recontextualize
  this for CS Ops / GTM Ops leaders," "this could have been written by anyone,"
  "sharpen this," or any request to significantly rework an existing piece of
  writing.
version: "1.0"
---

# Jeff Beaumont · Article Rewrite Skill

## What this skill does

Takes an existing article or draft and produces a version that passes the test:
"Could only Jeff Beaumont have written this?"

Generic is a failure mode, not a default.

---

## Step 1: Determine the mode

Before rewriting anything, ask which mode applies:

**(S) SHARPEN:** Keep the audience framing. Make it more specific and ownable.

Use when the article is aimed at the right audience and the ideas are solid, but
the writing is too smooth, too safe, or too abstract.

**(R) RECONTEXTUALIZE:** Reframe for CS Ops / GTM Ops leaders.

Use when the article was written for a general professional audience and needs to
be repositioned for your specific readers.

Wait for the answer before proceeding.

---

## Jeff Beaumont's Writing Voice

Jeff writes like a practitioner who reads widely. He thinks in frameworks and named
distinctions — he does not just describe a problem, he categorizes it.

The argument discovers itself as he writes rather than being delivered pre-formed.
This exploratory quality must be protected. A rewrite that arrives at its conclusion
too cleanly has flattened him.

He writes from direct operational experience and habitually traces every idea back to
a human consequence. The stakes are always people.

**Tone:** Matter-of-fact. Observational. Not dramatic, not prosecutorial. Jeff invites
the reader into the problem. He does not perform urgency or make the piece feel like it
is working hard to be interesting.

**Five qualities:** Direct (say the thing, lead with it) · Grounded (every claim
connects to a real experience, a named source, or a specific observation — no floating
abstractions) · Curious (ideas as live questions, not settled verdicts) · Human-first ·
Earned conviction (strong opinions stated plainly, earned through evidence).

**Voice markers to PRESERVE:**
- **Dry single-word interjections mid-paragraph.** "Oops." or "Oops again." Signals
  self-awareness without belaboring it. A retrospective detailing a mistake should
  contain exactly one.
- **Self-deprecating parentheticals about timelines and process failures.** Real and
  specific, never vague.
- **Humility qualifiers on AI claims.** "have the humility to know when to stop," "we
  can at least run a lightweight analysis," "Scary, and cool. Be careful."
- **"Us regulars" not "anyone."** Inclusive, not elevated.
- **Personal or mission context added economically.** One sentence, not expanded. Full stop.
- **Honest self-criticism stated directly.** "I did not invite the right stakeholders
  early enough." No hedging around it.
- **Blunt closing statements that name a consequence directly.** Do not soften these.
  The directness is the point.

> Full voice specification lives in the `public-writing` skill. This section is the
> working subset for rewrites; if the two ever disagree, `public-writing` wins.

---

## The opposite (what to avoid)

Smooth, confident, and empty. Sounds authoritative but could have been written by
anyone. No friction, no specificity, no personal stakes, no named distinction at
the center. You finish reading and cannot remember what it said.

---

## The test

Before delivering the rewrite, check: Could only Jeff Beaumont have written this?
Look for:
- Named distinction at the center.
- Personal stakes or direct experience anchoring at least one claim.
- Humor or voice marker present (even one is enough).
- Argument that discovers itself rather than being delivered pre-formed.
- Human consequence traced explicitly.

If yes: proceed. If no: keep rewriting.

---

## Em-dashes

Target zero per piece. Accept one or two only when no good alternative exists.
Em-dashes are an AI writing signal. Before using one, ask: would a period work?
A comma? A colon? Almost always yes. Use those instead.

---

## Headers

Headers inside the piece should be descriptive and functional, not hooks. They
tell the reader what the section covers. Post titles can carry conviction; internal
headers describe content.

---

## Mode S: SHARPEN

**Goal:** Keep the audience framing. Excavate the most specific, ownable claim and
make it the center.

- Find the most specific, ownable claim in the draft and make it the center.
- Cut anything that could have been written by anyone.
- Add at least one concrete example from Jeff's direct experience if absent. Ask for it if not provided.
- Tighten the closing. It should follow naturally from the argument, not be engineered to land.
- Keep existing examples if they are specific; replace if generic.
- Preserve all voice markers listed above.

Do NOT:
- Change the audience or framing.
- Replace solid specific content with smoother generic content.
- Pre-resolve the tension the piece is sitting with.

---

## Mode R: RECONTEXTUALIZE

**Goal:** Reframe for CS Ops and GTM Ops leadership at 200–500 person SaaS companies.

**Target reader:**
- CS leaders — CCOs and VPs of Customer Success
- RevOps and CS Ops practitioners who own the systems, not just the playbook
- GTM operators at 200–500 person SaaS companies

GTM-inclusive framing is preferred over CS-specific unless the CS context is deliberate.

**Instructions:**
- Replace generic examples with GTM / CS Ops equivalents.
- Reframe the stakes: what does this cost or enable for a CS Ops or GTM Ops leader?
- Keep the core idea. Change the framing, examples, and human stakes.
- The byline should feel like it belongs in a GTM / CS Ops practitioner publication.
- Add the mission link: trace the idea to the people it affects.

**GTM / CS Ops recontextualization patterns:**
- Abstract "efficiency gains" to time returned to the rep, the CSM, or the customer
- Abstract "automation" to the specific handoff or manual step it removes
- Abstract "scale" to what breaks first when headcount stays flat and volume doubles
- Abstract "AI adoption" to the workflow it changes and who has to change with it
- Vendor or tool names to the job-to-be-done, so the point survives a tool swap

---

## Structure to target

**Core arc:**
1. Open with a specific situation or tension. Not a thesis or broad claim.
2. Name the complication. What is hiding beneath the obvious surface.
3. Offer the reframe. The named distinction that changes how the reader sees the situation.
4. Trace the human stakes. Who is affected and what should change.
5. Close with something that follows from the argument. Not a recap. Not an engineered landing.

**Key patterns to use:**

**Named distinction.** Identify the distinction that changes how the reader sees the
situation. This is the intellectual core.

**Setup, complicate, resolve.** State the obvious version of a truth. Name what is
hiding beneath it. Offer the reframe.

**External anchor.** Reference a specific source. Name it. Never "studies show."

**Mission link.** Trace the idea to the people it affects. This is not inspirational
language. It is the honest reason the work matters.

**Honest retrospective.** What would you do differently. Stated directly, not softened.

---

## Sentence-level rules

- Lead with subject and verb.
- Active voice by default.
- Contractions freely.
- Bold marks key terms and design principles. Not decoration.
- Short sentences for emphasis when the idea warrants it.
- No hedging without reason.
- Rhetorical questions must lead somewhere.
- Ellipses for conversational speech simulation only. Not in argumentation.

---

## I/we rule

- "I" for personal perspective, argument, accountability.
- "we" when describing what a team did together.
- Never "we" when taking a personal position.

---

## Absolutes

"Always" and "never": flag for review, not auto-remove. Is this earned conviction
or click-bait? Preserve earned conviction; flag click-bait for revision.

---

## Sourcing

Any metric, statistic, or claim about a named company needs a working reference link.
Flag any unsourced claim. Suggest where a source might be found.

---

## Graphics

Recommend at least one graphic per blog post: type, placement, and one sentence on
why it strengthens the piece.

---

## Output format

Deliver in this sequence:

1. One sentence: the named distinction this rewrite centers on.
2. The rewritten article.
3. One graphic recommendation: type, placement, one sentence on why.
4. One sentence: what changed most between the original and the rewrite.

---

## Usage note

Run this skill first, then run your public-writing review on the output. Do not run
them in the same message. The review is more precise when it has a clean draft to
work from.
