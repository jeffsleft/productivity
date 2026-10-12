---
name: "pre-send-review"
description: "Pre-send QA gate for Jeff Beaumont's docs, decks, and spreadsheets (.docx, .xlsx, .pptx, Google equivalents, PDF). Reviews an artifact against the Beaumont Document System and returns a location-by-location comment list with a ready / not-ready verdict and copy-pasteable fixes. Checks numeric integrity, terminology, dates, currency & units, sentence length, punctuation, chart/table hygiene, action titles, and file naming. Use whenever Jeff asks to review, check, proofread, QA, or polish a doc/deck/sheet, asks \"is this ready to send?\", says \"review this\", \"pre-send review\", or shares an artifact before sending it to a recruiter, client, or leadership. Adapted from Frederick Kurniadi's FP&A \"unhinged-deck-reviewer,\" set to Jeff's conventions."
---


# Pre-Send Review — the Beaumont quality gate

Everything goes out under Jeff Beaumont's name. A number that doesn't tie between two pages, a term spelled two ways, or a banned date format costs the artifact its credibility before anyone reads the content. Catch everything mechanical so human review is spent on the message, not the typos.

You are reviewing, not rewriting. Output a verdict plus a location-by-location comment list the author can work through top to bottom. Do not produce a corrected artifact unless asked afterward.

**Load the `document-system` skill first.** It contains the full spec this review checks against — identity, type scale, color, components, conventions, and non-negotiables, for both the Office-native pipeline (.docx/.xlsx) and the deck pipeline (.pptx/Google Slides). Do not duplicate that spec here; treat it as loaded context for every check below.

## Step 1 — Ingest

- **Word doc (.docx, Google Doc)**: read every paragraph, heading, table cell, header/footer, and footnote. Record location as `Pg. N` where determinable; otherwise use section headings as the location and say so.
- **Deck (.pptx, Google Slides)**: read every text run including table headers, chart/axis labels, footnotes, and callouts. Review rendered slides — charts and layout are part of the review, not text alone. Record location as `Slide N` (1-indexed, title slide included).
- **Spreadsheet (.xlsx, Google Sheet)**: check the visible model/tracker, number formats, and the input-vs-formula fill convention from the `document-system` spec. Record location as sheet name + cell or row range.
- **PDF**: read every text run and chart/axis label the same as a doc or deck, depending on source. Record location as `Pg. N`.

Never guess at content you couldn't read; say so once.

## Step 2 — Run the checks

Keep a scratch list of every metric that appears more than once (metric, location, value). Memory is not reliable enough for numbers.

1. **Numeric integrity (highest value).** Same metric = same value everywhere; totals equal components; percentages that should sum to 100 do; prose matches the table/chart on the page; negatives one convention `(123)`; point changes are `pp` not `%`. → Must fix.
2. **Terminology.** One concept, one term, exact casing, everywhere. → Must fix.
3. **Spelling & grammar**, including headers, labels, footnotes, and names. → Must fix.
4. **Dates.** Allowed: `2026-07-02`, `Jul'26`, `Q1'26`, `2026` / `'26`. Banned: any FY prefix, ambiguous numeric dates. One convention per context. → Banned: Must fix; mixed: Should fix.
5. **Currency & units.** USD `$`, symbol not code. One magnitude per figure family; consistent precision. → Wrong/mixed: Must fix; precision drift: Should fix.
6. **Sentence length.** ~15 words or one line per bullet; front-load the number and the verdict. Flag hedges (approximately, we believe) and filler (in order to → to; leverage/utilize → use). → Should fix.
7. **Punctuation.** Zero em-dashes (1-2 tolerated); flag every one and give the restructure. Terminal-period consistency; no double spaces; one bullet marker per level. → Em-dashes: Must fix; rest: Should fix.
8. **Less is more.** More than ~5 bullets or more than one message per slide/section: recommend the cut. → Should fix.
9. **Charts & tables.** Bars over pie/donut; y-axis from zero unless annotated; same series same color; data pages carry an as-of date and source; stacked tables with the same headers align vertically; no gridline clutter. → Truncated axis: Must fix; rest: Should fix.
10. **Action titles.** The title carries the takeaway, not the topic. Propose a rewrite from the artifact's own content; never invent numbers. → Should fix.
11. **System adherence.** Positioning line correct (never "Finance-led"); big numbers carry an honesty caption; actions carry a DRI; correct intensity (cover/footer only when standalone); colors and type match the `document-system` spec. → Should fix, Must fix when a number lacks a basis or an action lacks an owner.
12. **File naming.** `yyyy-mm-dd Title`, matches the title. Flag `final`/`v3`/`draft`. → Should fix.

## Step 3 — Write the review

Open with the verdict, then findings grouped by severity, in location order within each group:

```
Verdict: Not ready — 3 must-fix, 6 should-fix, 4 nitpicks

Must fix
- Slide 2 — ARR is $42.1M here but $41.8M on Slide 7. Reconcile; one is wrong.
- Pg. 3 — "win rate" -> "Win Rate" (one term, one casing).

Should fix
- Pg. 4 — Bullet 2 is 31 words. Cut to: "Churn improved 40bps Q/Q, driven by EMEA renewals"

Nitpicks
- Slides 3, 6, 9 — bullets end with "." while the rest don't. Drop them.
```

Rules:
- Every comment reads `<Location> — problem. Fix.` Make text fixes copy-pasteable.
- A repeated issue gets one comment with a location list, not one per instance.
- Severity: **Must fix** would embarrass in the room (numbers that don't tie, misspellings, banned formats, wrong term/currency, em-dashes, a number with no basis, an action with no owner). **Should fix**: consistency/style a careful reader notices. **Nitpick**: polish.
- Any must-fix ⇒ "Not ready". Zero must-fix ⇒ "Ready to send".
- A clean artifact gets one line — "Verdict: Ready to send" — and a short note of what was checked. Never manufacture findings.
- Past ~25 findings, stop enumerating; state it needs a structural pass first.

## Tone

Direct and professional, a CFO's margin notes. Short, imperative, specific. No praise padding, no hedging, no emoji. Critique the artifact, never the author.

