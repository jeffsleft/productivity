---
name: "document-system"
description: "MANDATORY styling layer for every Word doc, Google Doc, PowerPoint/Slides deck, or Excel/Sheets spreadsheet produced for Jeff Beaumont — load this BEFORE the docx, pptx, or xlsx skill, every time, no exceptions for \"internal\" or \"quick.\" Applies to any strategy doc, proposal, write-up, persuasive plan, strategy readout, pitch, one-pager, financial model, or tracker. Triggers: \"make a doc / deck / proposal / one-pager / readout / model / tracker,\" \"draft a proposal,\" \"build a deck,\" \"put this in a document,\" \"create a spreadsheet,\" or any request ending in a .docx, .pptx, .xlsx, Google Doc, Google Slides, Google Sheet, or PDF export of any of those."
---


# The Beaumont Document System

Every doc, deck, and spreadsheet goes out under Jeff Beaumont's name. This system makes them uniform and credible before anyone reads a word.

**If a task is about to produce a .docx, .pptx, .xlsx, or a Google-equivalent file, this skill loads first — before docx/pptx/xlsx.** Those skills handle file mechanics (how to write a valid OOXML file); this skill governs what it looks like and how it's structured. Loading the format skill alone, without this one, is an off-system build.

**No silent format substitution.** If a task will produce a .docx, .xlsx, .pptx, or Google-equivalent, either build it as that exact format under this system, or ask first if the format is ambiguous. Never default to a different file type (plain HTML, a hand-built PDF, etc.) without Jeff's explicit go-ahead.

## Identity (never varies)

- Positioning line: **"GTM Operations & Strategy Leader"**. Never append "Finance-led" — that qualifier is retired.
- Monogram: charcoal circle, white "JB" mark. Use a PNG/raster version when embedding in Word or Excel — SVG doesn't embed reliably in Office files.
- Method line, usable as an epigraph: "I diagnose before I prescribe, build before I advise, and leave behind something the team can run without me."
- Type: all sans. Font fallback chain for Office files: **Inter** (if confirmed installed) → **Source Sans 3** → **Calibri** (safe default — assume this unless Jeff confirms otherwise). The only serif anywhere in the system is the monogram's "JB". No serif titles, ever.
- Color (hex, identical in docx and xlsx):
  - Charcoal `#2E2E2E` — cover/title-block background
  - Ink `#1C1C1C` — headings, big numbers
  - Body `#3A3733` — running text
  - Captions `#8A857D` — labels, honesty captions, footnotes
  - Hairlines `#E6E2DC` — thin rules, table borders
  - Paper `#FFFFFF`
  - **Accent — ink navy `#33495E` — the only accent color in the entire system.** Used sparingly: a kicker label, a short rule under a title, one header row, one highlighted figure per page. Never a second accent, never a highlighter fill.
  - Sheet-only: input-cell fill `#FCF7EC` / border `#E7DDC4`; health-status dots green `#4A8A63`, amber `#D0A13A`, red `#C25B4E`.

## Type scale

**Word (.docx):**

| Role | Size | Weight | Color |
|---|---|---|---|
| Cover title (Standard intensity only) | 24pt | Bold | White on charcoal box |
| H1 (doc title) | 20pt | Bold | Ink |
| H2 (section head) | 15pt | Bold | Ink, followed by a 3pt navy rule |
| H2 sub-line | 10.5pt | Regular | Caption gray |
| H3 | 12pt | Bold | Ink |
| Body | 11pt | Regular | Body, line spacing 1.5, never justified, always left-aligned |
| Kicker (e.g. "PROPOSAL") | 9.5pt | Bold, ALL CAPS | Accent navy |
| Big-number figure | 26pt | Bold | Ink |
| Big-number label | 10pt | Bold | Ink |
| Big-number honesty caption | 9pt | Regular | Caption gray — mandatory, see Non-negotiables |
| Table header | 8.5pt | Bold, ALL CAPS | Caption gray on white, or white on navy fill for DRI tables |
| Table body | 10pt | Regular | Body |
| Footer (Standard intensity only) | 8pt | Bold, ALL CAPS | "JEFF BEAUMONT" left / "GTM OPERATIONS & STRATEGY LEADER" right, muted gray `#A8A29A` |

Table borders: bottom-only hairlines (header 1pt `#D8D4CE`, body 0.5pt `#EFECE7`). **Never apply Word's built-in "Table Grid" style** — it draws all four sides of every cell and produces the gridline clutter this system forbids. Build tables unstyled and set explicit bottom-only cell borders instead.

**Excel (.xlsx):**

| Role | Size | Weight | Fill/Color |
|---|---|---|---|
| Title bar (row 1, merged) | 13pt | Bold | White text, charcoal fill |
| Title subtitle | 11pt | Regular | `#B8B3AA` text, charcoal fill |
| Column header row | 10pt | Bold | White text, navy fill |
| Row label (leftmost col) | 10pt | Bold | Ink |
| Formula-derived cell | 10pt | Regular, monospace (Consolas) | White fill |
| Hand-typed input cell | 10pt | Regular, monospace (Consolas) | `#FCF7EC` fill, `#E7DDC4` border |
| Basis/notes column | 9pt | Regular | Caption gray |

## Components

- **Kicker + title** — kicker line (accent navy, small caps) directly above the heading, then a short navy rule, then the title. Fixed order: kicker → rule → title.
- **Big-number callout + honesty caption** — figure (26pt bold ink) → label (10pt bold ink) → honesty caption (9pt caption gray, states the basis/conservatism, e.g. "Conservative case. When in doubt, excluded."). A big number with no honesty caption is off-system.
- **Diagnosis → Actions (always separated)** — two columns. Left labeled DIAGNOSIS (caption gray), right labeled ACTION (accent navy). Every action traces back to a named diagnosis item; never merge them into one paragraph.
- **DRI / owner table** — standard table, columns typically Action/Initiative → DRI → Due. Every row names a person or role in the DRI column. No blank DRI cells at delivery.
- **Pull-quote epigraph** — italic, 10pt, muted `#6B675F`, left border rule, indented. Used sparingly — the method line or a closing mission-link line, not decoration.

## Intensity

- **Standard** (default): external / standalone deliverables — recruiters, clients, anyone outside a team Jeff already leads. Full cover/title block, byline, monogram, footer.
- **Internal**: working docs for a team Jeff already leads. No cover, no footer, denser spacing, straight into content. Same system, lower ceremony — never an excuse to drop the type scale, color rules, or components above.

Ask which if unclear. Post-hire / post-contract readouts and proposals default Internal.

## Co-brand

Client engagements only: client logo and accent color slot into defined positions; type, grid, components, and tone stay Jeff's. Default is Jeff's brand alone. This mode is not yet fully specified for Office-native builds — flag to Jeff before attempting rather than improvising placement.

## Conventions (finance-grade hygiene)

- Currency USD, symbol not code: `$3.2M`, `$450K`. One magnitude per figure family — never mix `$3,400K` next to `$3.4M`.
- Consistent decimal precision within a context. Percentages: 0–1 decimal, pick one and hold it.
- Negatives in parentheses `(123)`, never a minus sign. Point changes are **pp**, never `%` (22%→32% is "+10pp," not "+10%").
- Dates: ISO `2026-07-02`, or `Jul'26`, `Q1'26`, `'26`. Always include the year suffix on quarter/period labels — `Q1` alone is incomplete. No FY prefix on dates. No ambiguous `07/02/26`. One convention per artifact.
- One term per concept, exact casing, everywhere in the artifact.
- **Numbers must tie**: same metric = same value everywhere; totals equal the sum of components; prose matches the table/chart on the same page. This is the single highest-value check.
- Charts: bars over pie/donut; y-axis from zero unless the truncation is explicitly annotated; same series keeps the same color throughout; every data page carries an as-of date and a source.
- Titles carry the takeaway, not the topic ("Renewal Operations: build the function the company never had," not "Renewal Operations Update").
- File names: `yyyy-mm-dd Title.ext`. The date is the version — never `final`, `v2`, `v3`, or `draft`.
- Excel number-format strings: millions with one decimal `"$"#,##0.0,,"M"`; thousands `"$"#,##0,"K"`; percent one decimal `0.0%`; pp delta `+0.0"pp";-0.0"pp"`; negative currency in parens `"$"#,##0.0,,"M";("$"#,##0.0,,"M")`.

## Non-negotiables

Diagnosis before actions — always separate sections, every action traces to a diagnosis item, never tactics disguised as strategy. Every number has a stated basis. Every action has a named DRI and a date. **Zero em-dashes** (1–2 tolerated only when no alternative exists) — check any generated labels or headers too, not just body prose. Contact info (name, monogram, footer) only on standalone deliverables — suppress on internal working docs. Room for 1–2 dry, witty lines per artifact — never forced, never at the cost of a credible surface.

## Build

- **Word doc → real `.docx`.** Use the `docx` skill for file mechanics (python-docx). Apply every color, size, and spacing value above — do not eyeball or approximate from memory.
- **Spreadsheet → real `.xlsx`.** Use the `xlsx` skill for file mechanics (openpyxl). Apply the Excel type scale and number-format strings above.
- **Slide deck → PowerPoint/Google Slides.** Use the `pptx` skill for file mechanics. Structure: charcoal section-opener slides (monogram + name + positioning line, kicker, large title, short navy rule) alternating with white content slides (one idea per slide, kicker + title, body content using the components above — big-number tiles, diagnosis/action pairs, DRI tables). One message per slide; no dense paragraphs on a slide.
- **Google Docs / Slides / Sheets:** identical design rules to their Office equivalents, built through whichever Google-side tool is in use.
- **PDF:** export from the native format (Word → PDF, Slides → PDF); never hand-build a PDF from scratch for something that's really a doc or deck.

Note: a fuller HTML-based visual reference for this system (with pixel-exact layouts and a dedicated deck-building tool) exists in Jeff's Claude Code local skill install. If a build in this environment doesn't have enough detail to match a specific layout, say so rather than guessing, and suggest the Claude Code version for pixel-exact work.

After building, run the `pre-send-review` skill against the finished artifact before calling it done.

