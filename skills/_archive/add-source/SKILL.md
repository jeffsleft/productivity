---
name: add-source
description: Fire when adding a research source to the library, pasting transcripts/reports/articles for extraction and filing to Notion Source Library
version: 1.0.0
---

# /add-source

## Trigger
User types `/add-source` followed by (or with pasted text in the same message) a transcript, report, article, or summary they want to add to the Source Library.

Alternatively, user may type `/add-source` with no text, in which case ask: "Paste the source text (or describe the source URL/title and key points) and I'll extract and file it."

## What This Skill Does
Takes unstructured source material — VC fund transcripts, macro reports, analyst write-ups, conference summaries, article excerpts — extracts structured metadata and key signals, and creates a tagged entry in the Notion Source Library. During future `/analyze-stock` or `/analyze-portfolio` runs, this library is queried and relevant entries are woven into the synthesis.

---

## Step-by-Step Execution

### Step 1 — Receive and Validate Input
- Check that the user has provided source text or a description of the source.
- If no text provided, prompt: "Paste the source content or summarize it, and I'll extract the key details."
- Confirm the source is not already in the library (search Source Library DB with the title or key phrases before creating).

### Step 2 — Extract Metadata

From the pasted text, extract:

| Field | Description |
|-------|-------------|
| **Title** | Name of the piece (report, article, fund letter, transcript) |
| **Author / Source** | Author name, fund name, publication, or "Unknown" |
| **Date** | Publication or transcript date. If not explicit, estimate from context or use today's date |
| **Source Type** | One of: VC Fund Letter / Macro Report / Analyst Note / Conference Transcript / News Article / Personal Note |
| **Known Bias / COI** | Any conflict of interest: e.g., "long NVDA", "sell-side bank", "fund manager with position in sector" |
| **Sectors Tagged** | List of sectors covered. Use the portfolio's sector vocabulary: Technology, Healthcare, Energy, Financials, Industrials, Consumer Discretionary, Consumer Staples, Utilities, Real Estate, Communication Services, Commodities, International, Multi-Sector |
| **Tickers Mentioned** | Any specific tickers referenced |
| **Themes Tagged** | Free-form thematic tags: e.g., AI Infrastructure, EV Demand, Interest Rates, China Risk, Dollar Strength, Defense Spending, Reshoring, Energy Transition |
| **Macro Stance** | Bullish / Bearish / Neutral on macro overall |
| **Key Bull Signal** | Single most important bullish takeaway (1–2 sentences) |
| **Key Bear Signal** | Single most important bearish takeaway (1–2 sentences, or "none identified") |
| **Core Insight** | The one thing that makes this source worth keeping: a specific data point, framing, or argument not widely covered elsewhere (2–3 sentences max) |
| **Staleness Date** | Set to 6 months from the source date. After this date, the source should be deprioritized in analysis queries |

### Step 3 — Confirm Before Filing

Display the extracted metadata to the user in a clean summary and ask:
"Does this look right? I'll file it to the Source Library. Reply 'yes' or correct any fields."

Wait for confirmation before proceeding to Step 4.

### Step 4 — Create Notion Source Library Entry

Create a new page in the Source Library DB (data_source_id: `[YOUR_SOURCE_LIBRARY_DS_ID]`).

Use these property mappings (check the Source Library schema first via `notion-fetch` on the collection URL to confirm property names match):

| Extracted Field | Notion Property |
|----------------|-----------------|
| Title | Page title |
| Author / Source | "Author" (text) |
| Date | "Date" (date) |
| Source Type | "Type" (select) |
| Sectors Tagged | "Sectors" (multi-select) |
| Tickers Mentioned | "Tickers" (text) |
| Themes Tagged | "Themes" (multi-select) |
| Macro Stance | "Macro Stance" (select) |
| Known Bias / COI | "Bias / COI" (text) |
| Staleness Date | "Stale After" (date) |

Include the full Core Insight, Key Bull Signal, and Key Bear Signal as body content (paragraph blocks) inside the page.

If a property doesn't exist in the schema, skip it rather than erroring — note any skipped fields to the user.

### Step 5 — Confirm and Report

Tell the user:
- The Notion Source Library page URL
- Which tickers and sectors it's tagged to (so they know it will surface in future analysis runs)
- The staleness date

---

## Important Constraints

- **Source Library schema**: The Source Library DB was created with basic schema. Some properties above (like "Themes", "Macro Stance") may not exist yet. Check the schema first. If a key property is missing, create it via `notion-update-data-source` before filing.
- **Duplicate check**: Always search before creating. If a very similar title exists, show the user the existing entry and ask if they want to update it instead of creating a new one.
- **No hallucination on source details**: If a field (like the date or author) cannot be found in the pasted text, say "not found" or ask the user rather than inferring.
- **Length**: Core Insight and Key Signals should be concise — 2–3 sentences each. Do not reproduce large chunks of the source in Notion; this is a signal extraction tool, not a document archive.
- **Source text privacy**: After filing, do not repeat back large portions of the pasted text. The extraction is the deliverable.
