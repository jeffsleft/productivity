---
name: draft-blog-post
description: "Draft a blog post for jeffreybeaumont.com from any input format — voice memo transcript, rough notes, prior conversation, or a detailed brief. Use this skill whenever Jeff wants to write a new post, start a series, or turn raw thinking into a structured draft. Triggers include: \"draft a post,\" \"write a blog post,\" \"I want to write about X,\" \"turn this into a post,\" \"help me develop this idea,\" or any request to produce original long-form content for a public audience. Always run this skill BEFORE the public-writing review. After drafting, hand off to the public-writing skill for voice and style review, then to publish-to-wordpress for publishing."
---

# Jeff Beaumont · Draft Blog Post Skill
### Version 1.0 — May 2026

## What this skill does

Takes raw input in any format and produces a WordPress-ready HTML draft that
passes the public-writing voice test. The process prioritizes getting the
argument right before polishing the prose.

This skill covers intake through draft. It does not cover voice review
(public-writing skill) or publishing (publish-to-wordpress skill).

---

## Input formats

Accept any of these without asking the user to reformat:

- Voice memo transcript (messy, repetitive, first-person)
- Rough notes or bullet points
- Prior conversation export or session summary
- A detailed written brief
- A single sentence: "I want to write about X"

If the input is a file, read it before asking questions.

---

## Step 1: Clarifying questions

Ask at minimum 10 clarifying questions before drafting. Target 98% confidence
that the argument, audience, structure, and key proof points are understood.
Do not draft until that threshold is reached.

Ask all questions in a single message, numbered. The user can answer in
shorthand ("1: yes, 2: see notes, 3: no"). Do not drip questions one at a time.

**Required question areas — cover all of these:**

1. **Argument** — What is the single sharpest claim this post makes? What would
   a skeptical reader push back on?

2. **Named distinction** — Is there a framework, category, or named contrast at
   the center? (e.g., efficiency vs. effectiveness vs. greenfield; bundling vs.
   unbundling) If not, what is the intellectual core?

3. **Target reader** — Who specifically is this for? CS leaders? GTM operators?
   Recruiters? A general professional audience? What do they already know and
   what do they need to be convinced of?

4. **Proof points** — What concrete examples, personal experiences, or operator
   signals anchor the argument? At least one should come from Jeff's direct
   experience. If none exist yet, flag it and ask.

5. **Series or standalone** — Is this part of a series? If so: what posts come
   before and after, what does each post establish, and how does this one
   advance the argument?

6. **Forwarding line** — If this is part of a series, what is the one-sentence
   forwarding line that closes this post and sets up the next? Must be in Jeff's
   voice, drawn from his actual words, not manufactured.

7. **Research needed** — Are there external sources, frameworks, or named
   thinkers this post should reference? Flag any claims that will need sourcing
   (metrics, company examples, named studies).

8. **Tone and register** — Is this analytical and structural (like the bundling
   or hurdle rate posts)? Personal and reflective? Practical and instructional?

9. **Length and depth** — How long should this be? Short (600–900 words),
   medium (900–1400), or long (1400+)?

10. **Graphic** — Every post requires a graphic specification. What visual best
    reinforces the argument? If the user doesn't know yet, suggest three options
    based on the content.

Add additional questions beyond these ten if gaps remain. Do not proceed until
the argument is clear enough to state in one sentence.

---

## Step 2: Research identification

Before drafting, surface any claims that need external support.

Flag:
- Any statistic or metric that will need a source
- Any named company, product, or case study that requires verification
- Any framework or thinker cited (Rumelt, Rogers, Almquist, etc.) that should
  be attributed correctly
- Any historical claim (dates, events, consolidation figures)

Do not invent sources. If a claim is plausible but unsourced, label it
`[Inference]` in the draft and note what kind of source would verify it.

If web search is available and sources are needed, offer to search before
drafting.

---

## Step 3: Structural outline

Before writing prose, produce a structural outline:

- Working title
- Subtitle (optional)
- One-sentence argument statement
- Section headers with one-sentence description of each section's job
- Where the named distinction sits in the structure
- Where the proof points land
- Forwarding line (if series)
- Graphic specification

Ask for approval or adjustments before proceeding to prose. This is the cheapest
moment to change the architecture.

---

## Step 4: Draft the post

Write in WordPress-ready HTML body content format:

- No `<html>`, `<head>`, or `<body>` wrapper tags
- `<h1>` for post title, `<h2>` for sections, `<h3>` for sub-sections
- `<p>` for body text
- `<sup><a href="#fnN" id="refN">N</a></sup>` for inline footnote references
- `<ol>` with `<li id="fnN">` for footnotes at the end
- `<hr />` for section breaks

**Series posts also include:**
- Series header at top: `<p><em>This is part of <strong>[Series Name]</strong>...
  <a href="[URL]">Start at Post 1</a>...</em></p><hr />`
- Forwarding line at bottom before footnotes: `<p><em>Next in this series:
  <a href="[URL]">[Post Title]</a> — [one sentence teaser].</em></p>`
- Series nav at very bottom: `<p><em>The series: Post 1 · Post 2 · ...</em></p>`

**Structural arc to follow:**

1. Open with a specific situation, moment, or tension — not a thesis or broad
   claim. Earn the argument through detail.
2. Name the complication. What is hiding beneath the obvious surface.
3. Offer the reframe. The named distinction that changes how the reader sees it.
4. Trace the human stakes. Who is affected and what should change.
5. Close with something that follows from the argument. Not a recap. Not an
   engineered landing.

**Draft discipline:**

- Lead with subject and verb.
- Active voice by default.
- Contractions freely.
- Short sentences for emphasis when the idea warrants it.
- No hedging without reason.
- Em-dashes: target zero. Use period, comma, colon, or semicolon instead.
  Accept one or two only when no good alternative exists.
- Bold marks key terms and named distinctions. Not decoration.
- Rhetorical questions must lead somewhere.
- Do not open with a broad claim that could have been written by anyone.

---

## Step 5: Graphic specification

Every post requires a graphic specification before handoff. Include at the end
of the draft:

```
---
GRAPHIC SPEC
Type: [diagram / chart / 2x2 / timeline / comparison table / cycle / other]
Placement: [after intro / after [section name] / end of post]
What it shows: [one sentence describing what the visual communicates]
Why it matters: [one sentence on why this visual strengthens the argument]
Graphic file must be PNG or JPEG to upload to Wordpress
---
```

If the graphic spec was agreed in Step 1, confirm it here. If not, propose
three options and ask which to include.

---

## Step 6: Post-draft checklist

Before handing off to public-writing, check:

- [ ] Named distinction is at the center, not in a side paragraph
- [ ] At least one proof point from Jeff's direct experience
- [ ] Argument discovers itself as the post progresses — not delivered pre-formed
- [ ] Human stakes traced explicitly somewhere in the post
- [ ] Forwarding line present (if series) and in Jeff's voice
- [ ] Graphic spec included
- [ ] Em-dashes: 0–2 total
- [ ] No inline-footnote claims left unsourced or unlabeled `[Inference]`
- [ ] Could only Jeff Beaumont have written this? If no, flag what to fix

---

## Step 7: Handoff

After draft is complete and checklist passes, state:

"Draft complete. Next step: run the **public-writing** skill to review voice and
style. After that, use the **publish-to-wordpress** skill to create the WordPress
draft."

Do not run both skills in the same message. Public-writing review is more
precise when it has a clean draft to work from.

---

## Anti-patterns to avoid

| Pattern | Why it fails |
|---|---|
| Opening with a broad thesis | Buries the argument before it's earned |
| Examples that could fit any post | Generic is a failure mode |
| Forwarding lines written by Claude, not Jeff | They won't sound like him |
| Proof points with no named source or personal anchor | Claims without stakes |
| Graphic spec left vague | "A diagram showing the framework" is not a spec |
| Em-dashes connecting clauses | AI writing signal |
| Insights section that recaps instead of analyzes | Describes what happened, not what it means |

---

## Series management

When drafting for a series:

- Read all existing posts in the series before drafting the new one to maintain
  argument continuity and avoid repeating proof points.
- Track which named distinctions and frameworks have already been introduced.
  Do not re-explain them — reference them by name and link.
- The series nav links at the bottom must be updated across all posts when a
  new post is added. Flag this as a follow-up task.