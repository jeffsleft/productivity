---
name: voice-engine
description: Draft or rewrite Jeff Beaumont's writing in his actual voice — not the trained AI register every model defaults to. Covers five tones (blog, cover letter, internal, email, LinkedIn). Uses Jeff's corpus-derived voice fingerprint, a two-pass Scribe draft, and a four-agent gate (Inspector, Weaver, Critic, Miller). Invoke whenever Jeff wants to draft or rewrite a blog post, cover letter, internal document, or email — or when he says "draft this," "rewrite this in my voice," "voice engine," or "run the engine on this."
version: 1.0.0
---

# Voice Engine Skill

You are running the Voice Engine — a multi-agent system that drafts writing in Jeff Beaumont's actual voice. Your job in this skill is to be the interface: gather what you need, orchestrate the engine, surface the output.

The engine lives at `~/Projects/voice-engine/`. You orchestrate it through Claude Code's Agent tool.

---

## When this skill runs

This skill runs when Jeff wants to:
- Draft a new blog post, cover letter, internal doc, or email from a brief or rough notes
- Rewrite an existing draft "in his voice"
- Run a specific piece through the gate (Inspector/Weaver/Critic/Miller) without a full redraft
- Check whether something "sounds like Jeff"

It does NOT run for:
- Editing that's purely structural (use the `internal-writing` or `public-writing` skills)
- StoryBrand strategy (use `/story-brand` first, then come back here with the BrandScript output)

---

## Step 1: Identify what Jeff has

Before building the brief, figure out what Jeff is working with:

**If he has rough notes or a voice memo transcript:**
- Extract the core claims and main point from the notes
- Ask what tone and audience
- Build the brief from there

**If he has an existing draft:**
- Read it and identify the tone, word count, and main claims
- Ask if he wants a full redraft or a gate review only
- If gate review only: skip to Step 4

**If he has a BrandScript from `/story-brand`:**
- The BrandScript output maps directly to the brief:
  - Hero's desire → first claim
  - Villain/problem → what's at stake
  - Guide/empathy → background_facts
  - Plan → structure of the piece
  - CTA → closing move
  - Stakes + transformation → anti_claims guidance
- Ask Jeff to confirm intent = persuasive and paste BrandScript into background_facts

**If he has nothing yet:**
- Walk him through the brief fields below (Step 2)

---

## Step 2: Build the brief

The brief is a YAML file (`briefs/YYYY-MM-DD-slug.yaml`). You will build it interactively.

**Required fields — ask for these:**
- `tone`: blog | cover-letter | internal | email | linkedin
- `intent`: informative | persuasive | internal | email
- `claims`: the 1–3 main assertions the piece must make (in priority order)
- `word_count_target`: integer — typical ranges: blog 600–1200, cover letter 300–500, internal 200–800, email 100–400

**For rewrites of existing posts — always ask:**
- `images_to_preserve`: does the original post have images? If yes, get a description of each, where it appears, and any caption text. Scribe will insert `[IMAGE PLACEHOLDER: ...]` markers so nothing is silently dropped. WebFetch strips images entirely — if you don't capture them in the brief, they're gone from the draft.

**Optional but important — surface from context or ask:**
- `title`: working title or subject line
- `target_audience`: one sentence describing the reader
- `accomplishments_to_surface`: keywords that pull from Jeff's Accomplishments Inventory (e.g., `renewal-ops`, `nrr`, `gitlab`, `mercy-ships`, `greenfield`, `scaling`, `ai`, `finance`)
- `background_facts`: specific facts to include that the model can't derive from the corpus (names, dates, metrics, BrandScript output)
- `anti_claims`: what NOT to say ("do not mention salary history," "don't frame as CS-only")

**Show Jeff the completed brief before proceeding.** Ask for a quick "looks right" before running the engine. One confirmation, no back-and-forth.

Example brief:
```yaml
tone: blog
intent: informative
title: "Systems, Not Playbooks"
claims:
  - "Hiring managers don't want someone who ran a successful ops motion at a similar company — they want someone who can read a new situation and build something that fits."
  - "The difference between replication and translation is judgment, not experience."
target_audience: "CS ops and GTM ops practitioners at 200–500 person SaaS companies"
word_count_target: 900
accomplishments_to_surface:
  - gitlab
  - greenfield
  - cs-ops
anti_claims:
  - "Do not frame as job search advice — frame as a practitioner insight"
background_facts: []
```

---

## Step 3: Run the engine

Once the brief is confirmed, invoke the Claude Code Agent to run the pipeline.

**Delegate to a `gemini-builder` agent (for file writes):**

```
Run the Voice Engine drafting pipeline for this brief.

Project root: ~/Projects/voice-engine/
Brief path: briefs/[YYYY-MM-DD-slug].yaml

Steps:
1. Write the brief YAML to the path above (content below)
2. Run: python3 scripts/draft.py briefs/[YYYY-MM-DD-slug].yaml
3. Return the full draft output and gate summary

Brief content:
[paste the brief YAML here]
```

**What comes back:**
- The draft file path (e.g., `drafts/2026-05-24-systems-not-playbooks-draft.md`)
- Gate summary: Inspector PASS/FAIL, Weaver verdict, Critic verdict, Miller verdict (if persuasive)
- Individual gate report paths for any FAIL/REVIEW/REVISE verdicts

If the engine is not available (Cowork context without Code access), run Scribe manually — see the Fallback section at the bottom of this skill.

---

## Step 4: Surface the output

Once the engine returns, surface results in this order:

**1. Gate summary first** — show the verdict line before the draft:
```
Inspector: PASS | Weaver: PASS | Critic: REVIEW | Miller: N/A
```

**2. If Critic is REVIEW or REVISE**, show the flagged sentences before the full draft:
```
Critic flagged 4 sentences:
  [LINE] "This is especially important because..." | REASON: structural-tell
  [LINE] "It is worth noting that..." | REASON: structural-tell
  [LINE] "This demonstrates meaningful impact..." | REASON: register-tell
  [LINE] "In many organizations, leaders find that..." | REASON: specificity-tell
```

**3. Show the full Pass 2 prose draft** (skip Pass 1 Bones unless Jeff asks for it)

**4. If Inspector FAIL**, show the violations before the draft and note they must be fixed before delivery

**5. Ask:** "Want me to run a revision pass on the Critic-flagged sentences, or take it from here?"

---

## Step 5: Revision loop (if needed)

If Jeff wants a revision pass on flagged sentences:

Feed the Critic annotations back into Scribe. Delegate to a Code Agent:
```
Revise the following sentences in the draft at [draft_path].
These were flagged by Critic as AI-tells. Revise each one to match Jeff's voice.
Do not change surrounding prose. Return only the revised sentences with their locations.

Critic annotations:
[paste the flagged sentences and reason codes]

Fingerprint constraints (from voice.yaml):
- avg sentence length 10–18 words
- em-dash max 0.921/100w
- hedging max 0.503/100w
- forbidden words: [paste forbidden list]
```

---

## Step 6: Export to .docx

When Jeff is satisfied with the draft:

```
Run: pandoc drafts/[filename].md -o drafts/[filename].docx
```

If pandoc is not available in the current environment, provide the markdown text directly for Jeff to paste into Word or Google Docs.

---

## Tone quick-reference

| Tone | Avg sentence length | Dominant opener | Em-dash rate |
|---|---|---|---|
| blog | 13.9w | direct claim or dialogue | 0.54/100w |
| cover-letter | 24w | "I" sentences | 1.28/100w |
| internal | 22.2w | "The" or "This" | 0.79/100w |
| email | 25.5w | direct orient or "We" | 0.23/100w |

---

## Accomplishments Inventory keyword reference

Common tags that retrieve relevant bullets:

| Keyword | What it pulls |
|---|---|
| `gitlab` | GitLab CS Ops, PROVE engine, Digital CS, Renewal Ops, PS tracking |
| `mercy-ships` | HIS program, economic impact analysis, OpEx automation |
| `renewal-ops` | 120-day renewal motion, loss-code taxonomy, segmentation |
| `nrr` | NRR/GRR results, retention infrastructure |
| `greenfield` | Built-from-zero stories at Riskalyze, RightCapital, GitLab, Mercy Ships |
| `ai` | LLM-driven workflows, AI-native operations, automation |
| `finance` | Finance ops, budget proposals, treasury, economic modeling |
| `cs-ops` | CS operations broadly |
| `consulting` | Three NDA consulting engagements |
| `scaling` | Org growth, headcount planning, capacity modeling |

---

## Fallback: Scribe without the engine (Cowork-only context)

If the Voice Engine pipeline is not accessible (no Code Agent available), run Scribe manually in this session:

1. Load the brief from Step 2
2. Ask Jeff to confirm: "Running Scribe in this session — no gate checks available. Want to proceed?"
3. Invoke Scribe's two-pass process:

**Pass 1 (Bones):**
Map claims to proof. Identify opening move and closing move. Flag any gaps.

**Pass 2 (Prose):**
Draft the piece using Jeff's voice rules:
- Sentence length: avg 14w, 41% under 10w, 11% over 25w
- Claims before explanations
- Short sentences as punctuation
- Em-dashes for pivots (0.5–0.9/100w)
- No hedging clusters
- No AI-tell vocabulary (leverage, utilize, ensure, scalable, actionable, ecosystem, alignment)
- Tone-specific: use the quick-reference table above

Note in your output: "Gate not run — Critic and Inspector checks require the Code pipeline. Review output manually against the forbidden word list before delivery."

---

## Key decisions (do not re-derive)

- **One voice, four tones.** Not four voices. The fingerprint is shared; the tone overlay shifts by format.
- **LinkedIn uses blog tone.** No dedicated LinkedIn overlay in V1.
- **Miller only for persuasive.** If `intent` is not `persuasive`, Miller does not run and `/story-brand` is not required upstream.
- **Markdown internally, .docx at export.** Never deliver raw markdown to Jeff as a final artifact.
- **BrandScript goes in `background_facts`.** When `/story-brand` runs first, paste its output there. Do not modify the brief schema.
