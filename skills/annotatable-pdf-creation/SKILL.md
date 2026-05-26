---
name: annotatable-pdf-creation
description: Create annotatable PDFs optimized for iPad annotation (GoodNotes/Notability). Use this skill whenever the user wants to convert articles, tweets, blog posts, or any written content into a formatted PDF with wide left margins for annotations, justified body text, navy section headers, and embedded images. Trigger on phrases like "convert to PDF," "make this annotatable," "create a PDF for annotation," "turn this into a PDF," or when the user uploads content they plan to annotate.
---

# Annotatable PDF Creation Skill

This skill automates the creation of PDFs formatted specifically for iPad annotation with GoodNotes, Notability, or similar apps. The output format is consistent across all PDFs and optimized for margin-based note-taking.

## Standard PDF Format (Apply to All)

**Dimensions & Layout:**
- Page size: Letter (8.5" × 11")
- Left margin: 1.2" (for handwritten notes)
- Right margin: 0.75"
- Top/bottom margins: 0.75"
- Text alignment: Justified (except headers/captions)

**Typography:**
- Body text: 11pt Helvetica, leading 16pt
- Main title: 18–26pt Helvetica Bold, navy (RGB 0.118, 0.153, 0.38)
- Section headers (h2): 14–15pt Helvetica Bold, navy
- Subsection headers (h3): 13pt Helvetica Bold, navy
- Captions: 9pt Helvetica Italic, gray
- Metadata (date, source): 10pt Helvetica, gray

**Colors:**
- Navy headings: `Color(0.118, 0.153, 0.38)`
- Gray text (metadata/captions): `Color(0.4, 0.4, 0.4)`
- Body text (dark): `Color(0.173, 0.173, 0.173)`

**Spacing:**
- After titles: 6–8pt
- After subtitles: 14–16pt
- Between body paragraphs: 12pt
- Between sections: 0.15–0.2 inches
- Image to caption: 0.05"
- After caption: 0.12–0.14"

## Workflow: Single PDF from Content

For articles, tweets, or multi-section content that should be **one PDF**:

1. **Identify content type and structure**
   - Is this a tweet thread? → Extract and order sequentially
   - Is this an article with sections? → Use section headers (h2/h3)
   - Does it have images? → Determine placement by caption/context
   - Is it a single quote or short piece? → Keep simple, minimal headers

2. **Extract and organize content**
   - Remove markup, links, and irrelevant metadata (timestamps, share buttons, etc.)
   - Preserve: author names, dates, quoted text, section titles, image captions
   - Preserve: emphasis (bold, italic) using `<b>` and `<em>` tags
   - Keep verbatim wording—do not summarize or edit

3. **Handle images**
   - Embed images in their logical position within the text flow
   - Use caption text exactly as provided in the source
   - Image width: typically 5.5 inches (leaves ~0.2" bleed on each side within margins)
   - Maintain aspect ratio; let height scale proportionally
   - If image source URL is provided, check if it's fetchable; use local upload path if already provided

4. **Build PDF with ReportLab**
   - Use Python + ReportLab (PIL for image handling if needed)
   - Save to `/mnt/user-data/outputs/[DescriptiveName]_Annotatable.pdf`
   - Test file existence and print success message with checkmark

5. **Present file to user**
   - Use `present_files` tool
   - One brief closing statement: describe what the user received

## Workflow: Multiple PDFs from Separate Items

For tweet threads, job postings, or distinct content pieces that should be **separate PDFs**:

1. **Clarify scope with user**
   - Confirm: "Create one PDF per item, or combine into one?"
   - If combining: treat as single-PDF workflow above
   - If separate: proceed with multiple PDFs

2. **Create individual PDFs**
   - One file per content piece
   - Filename: `[Author]_[Topic]_Annotatable.pdf` or `[Company]_[JobTitle]_Annotatable.pdf`
   - Reuse standard format; scale to content (short tweets = shorter PDF, long articles = multi-page)

3. **Present all files**
   - Use `present_files` with array of all paths
   - List them with one-line descriptions

## Example: Tweet Conversion

**Input:** Single tweet from person X

**Output:** `/mnt/user-data/outputs/[FirstName_LastName]_[Topic]_Annotatable.pdf`

Structure:
```
[Author Name] (title_style)
@handle · X/Twitter (subtitle_style)
[Blank space]
[Tweet text, paragraph by paragraph] (body_style)
```

## Example: Article with Images

**Input:** Full article with 2–3 embedded images

**Output:** `/mnt/user-data/outputs/[Publication]_[Title]_Annotatable.pdf`

Structure:
```
[Title] (title_style)
[Publication] · [Date] (subtitle_style)
[Blank space]

[Section Header] (head2_style)
[Subsection Header] (head3_style)
[Body paragraphs]
[Image 1, caption]
[More body paragraphs]
[Image 2, caption]
[More body paragraphs]
```

## Code Template (ReportLab)

```python
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import ParagraphStyle, getSampleStyleSheet
from reportlab.lib.units import inch
from reportlab.lib.enums import TA_LEFT, TA_JUSTIFY, TA_CENTER
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Image
from reportlab.lib.colors import Color

navy = Color(0.118, 0.153, 0.38)
gray = Color(0.4, 0.4, 0.4)
dark = Color(0.173, 0.173, 0.173)

# Define styles (see below for full template)

doc = SimpleDocTemplate(
    "/mnt/user-data/outputs/[Filename]_Annotatable.pdf",
    pagesize=letter,
    leftMargin=1.2*inch,
    rightMargin=0.75*inch,
    topMargin=0.75*inch,
    bottomMargin=0.75*inch
)

story = []
# Add content to story
story.append(Paragraph("Title", title_style))
story.append(Spacer(1, 0.1*inch))
# ... more content ...
story.append(Image('/path/to/image.png', width=5.5*inch, height=auto))
story.append(Paragraph("Caption", caption_style))

doc.build(story)
print("✓ [Filename]_Annotatable.pdf")
```

## Style Definitions (Complete)

Keep these consistent across all PDFs:

```python
title_style = ParagraphStyle(
    'CustomTitle',
    parent=styles['Heading1'],
    fontSize=18,  # or 20–26 for longer articles
    leading=22,
    textColor=navy,
    spaceAfter=6,
    alignment=TA_LEFT,
    fontName='Helvetica-Bold'
)

subtitle_style = ParagraphStyle(
    'Subtitle',
    parent=styles['Normal'],
    fontSize=10,
    leading=12,
    textColor=gray,
    spaceAfter=14,
    alignment=TA_LEFT,
    fontName='Helvetica-Oblique'
)

body_style = ParagraphStyle(
    'CustomBody',
    parent=styles['Normal'],
    fontSize=11,
    leading=16,
    textColor=dark,
    spaceAfter=12,
    alignment=TA_JUSTIFY,
    fontName='Helvetica'
)

head2_style = ParagraphStyle(
    'Head2',
    parent=styles['Heading2'],
    fontSize=14,
    leading=16,
    textColor=navy,
    spaceAfter=10,
    spaceBefore=12,
    alignment=TA_LEFT,
    fontName='Helvetica-Bold'
)

head3_style = ParagraphStyle(
    'Head3',
    parent=styles['Normal'],
    fontSize=13,
    leading=15,
    textColor=navy,
    spaceAfter=8,
    spaceBefore=10,
    alignment=TA_LEFT,
    fontName='Helvetica-Bold'
)

caption_style = ParagraphStyle(
    'Caption',
    parent=styles['Normal'],
    fontSize=9,
    leading=11,
    textColor=gray,
    spaceAfter=14,
    alignment=TA_CENTER,
    fontName='Helvetica-Oblique'
)
```

## Handling Images

- **Single image:** Embed in logical flow; use caption from source or infer
- **Multiple images:** Embed each where it logically belongs in the text
- **Image sizing:** Width 5.5", let height scale (maintains aspect ratio)
- **Captions:** Always include, sourced from article/source or inferred from context
- **Fallback:** If local path unavailable, note in PDF as `[Image: description]`

## File Naming Convention

- **Tweets:** `[FirstName_LastName]_[Topic]_Annotatable.pdf`
- **Articles:** `[Publication_or_Author]_[Title_or_Topic]_Annotatable.pdf`
- **Job postings:** `[Company]_[JobTitle]_Annotatable.pdf`
- **Combined:** `[Description]_Annotatable.pdf`

All files save to `/mnt/user-data/outputs/`

## Edge Cases

**What if content is paywalled?**
- User must paste the full text or screenshot; you cannot fetch paywalled pages
- Proceed with provided content as-is

**What if images are referenced but not provided?**
- Ask user to upload or provide image paths
- Proceed without images if user confirms, noting `[Image not available]` in PDF

**What if content has metadata/timestamps that should be included?**
- Include only if relevant (publication date, author, source)
- Omit: view counts, timestamps, share buttons, ads

**What if it's a thread spanning multiple tweets?**
- Confirm with user: one combined PDF or separate files per tweet?
- If combined: use subsection headers for each tweet or order sequentially
- If separate: create individual PDFs, present as array

## Testing & Validation

After building a PDF:
1. Verify file exists at expected path
2. Print success message with checkmark
3. Use `present_files` to make it available
4. Note to user: "Ready for annotation on iPad"

---

**Summary:** This skill takes any text-based content (tweets, articles, blog posts, job postings) and transforms it into a beautifully formatted, margin-rich PDF optimized for GoodNotes/Notability annotation. The format is consistent, the margins are generous, and the typography is clean and readable.
