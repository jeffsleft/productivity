---
name: blog-graphic-design
description: >
  Design and render blog post graphics as PNG files for jeffreybeaumont.com. Use
  this skill any time a blog post needs a visual — decision flow diagrams, comparison
  tables, 2x2 grids, framework illustrations, or any chart or diagram. Trigger on
  phrases like "create a graphic," "make a diagram," "build a chart," "I need a
  visual," "add a graphic to the post," or any time a blog post draft contains a
  GRAPHIC SPEC comment. Also trigger proactively when editing a post and noticing
  that a graphic spec exists but no PNG has been produced. Always output PNG files
  (never inline SVG in the HTML post). Always follow the construction rules in this
  skill before writing any SVG code.
---

# Blog Graphic Design Skill

## Purpose

Produce clean, professional PNG graphics for Jeff's blog posts. The goal is a
graphic that looks like it was made by a designer — not a quick script. The three
most common failure modes are: clipped content (viewBox too small), cramped text
(insufficient padding), and illegible labels (font too small or colors too similar).
This skill exists to prevent those.

---

## Toolchain

Always render SVG → PNG using `cairosvg` via Python. Never output raw SVG into
the HTML post file — WordPress does not render inline SVG reliably.

```python
import cairosvg
cairosvg.svg2png(bytestring=svg_string.encode(), write_to='output.png', scale=2.5)
```

Scale `2.5` produces sharp retina-quality images. Do not go below `2.0`.

Save PNG files to the same directory as the HTML post file. Use descriptive names:
`graphic-post3-decision-flow.png`, `graphic-post3-company-comparison.png`.

Reference in the HTML post as:
```html
<!-- Upload to WordPress Media and replace src with CDN URL -->
<figure style="margin: 2em 0;">
<img src="graphic-post3-decision-flow.png" alt="[descriptive alt text]"
     style="width:100%;max-width:740px;display:block;margin:0 auto;" />
<figcaption style="text-align:center;font-size:0.85em;color:#666;
  margin-top:0.5em;font-style:italic;">[Caption text]</figcaption>
</figure>
```

---

## Font

Always use: `font-family="Georgia, 'Times New Roman', serif"`

This matches jeffreybeaumont.com's body font and makes graphics feel native to
the site rather than pasted in.

---

## Color Palette

Use this fixed palette across all blog graphics. Consistency matters — a reader
who sees multiple posts should feel the visuals belong to the same system.

| Role | Hex | Use |
|------|-----|-----|
| Navy (primary) | `#1B3A5C` | Top-level headers, most important boxes |
| Mid blue | `#2A6496` | Secondary headers, diagnosis/process boxes |
| Steel blue | `#3A5F8A` | Action or option boxes (neutral) |
| Deep blue | `#1B4F8A` | Option boxes (redeployment, positive action) |
| Teal | `#2A8F7E` | Option boxes (restructure, change) |
| Slate | `#5a6a7a` | Neutral/passive options (do nothing, caution) |
| Light bg row | `#f4f7fb` | Alternating table rows |
| Caption gray | `#9aabb8` | Footer notes, captions |
| White | `#ffffff` | Box backgrounds, text on dark |

**Text on dark backgrounds:**
- Bold labels: `fill="#ffffff"`
- Secondary text: use a light tint of the box color (e.g., `#c0daf4` on `#2A6496`)
- Example/label text: use a mid tint (e.g., `#90c0e8` on `#1B4F8A`)

**Never use black (`#000000`) or pure gray for text inside colored boxes.** Always
use white or a tinted variant.

---

## ViewBox Sizing Rules

**Calculate the canvas from the content — never fit content to a fixed canvas.**

This is the most important rule. Clipping happens when the viewBox is set first
and content overflows it. Instead:

1. Sketch the layout in comments first
2. Tally the total height: sum all element heights + gaps + top/bottom margin
3. Set the viewBox to that total

### Height accounting formula

```
total_height = top_margin (20px)
             + sum(element_heights)
             + sum(gaps_between_elements)
             + bottom_margin (30px for footer note, else 20px)
```

**Standard element heights at 1x:**
- Single-line header box: 54px
- Two-line header box (title + subtitle): 72px
- Decision diamond: height = 2 × half-span (e.g., 160px wide = 160px tall)
- Option box (title + 2 body lines + example): 90px
- Option box (title + 1 body line): 68px
- Table data row: 76–90px depending on line count
- Arrow + gap between elements: 30–50px
- Footer note: 30px

Add 20px bottom margin past the last element before setting viewBox height.

### Width accounting

- Full-width post graphic: `800` viewBox width
- Decision flow with side boxes extending to canvas edges: `800`
- Comparison table: `780`

If side boxes must reach x=0 on the left, the diamond center must be placed
far enough right that diagonal arrows to bottom-left boxes don't go off-canvas.

---

## Box Padding Rules

**Minimum internal padding: 16px between text and box edge at 1x.**

### Text positioning inside boxes

For a box with a bold title + 2 body lines + 1 example line:
```
title_y    = box_y + 26
body1_y    = box_y + 46
body2_y    = box_y + 62
example_y  = box_y + 76
```
Box height must be at least `90px` in this case.

For a box with just a bold title + 1 body line:
```
title_y  = box_y + 23
body_y   = box_y + 43
```
Box height: `68px` minimum.

**General rule:** `first_text_y = box_y + (box_height × 0.32)` for single-line
labels. For multi-line, start the first line at `box_y + 26` and space lines 18px.

**Never position text within 8px of a box edge (top, bottom, left, or right).**

---

## Typography Hierarchy

| Element | Size | Weight | Color |
|---------|------|--------|-------|
| H1 box label | 15px | bold | `#ffffff` |
| H2 box label | 13px | bold | `#ffffff` |
| Body text in box | 11px | normal | light tint of box color |
| Example/credit line | 10px | normal | mid tint of box color |
| Footer caption | 10px | normal italic | `#9aabb8` |
| Table column header | 12px | bold | `#ffffff` |
| Table cell name | 13px | bold | `#1B3A5C` |
| Table cell body | 10–11px | normal | `#444` |

**Never go below 9px.** At 2.5x scale that's 22.5px rendered — borderline legible.
10px at 2.5x = 25px, which is the safe minimum.

---

## Arrow Design

Use `<line>` + `<polygon>` for arrows. Never use `<marker>` — cairosvg renders
markers inconsistently.

**Standard downward arrow:**
```svg
<line x1="400" y1="92" x2="400" y2="128" stroke="#9aabb8" stroke-width="2"/>
<polygon points="393,122 400,136 407,122" fill="#9aabb8"/>
```

The arrowhead tip should land exactly at the destination box's top edge.
The polygon's base (the two side points) should sit 14px above the tip.

**Arrow clearance:** Leave at least 20px gap between a box edge and the start
of the next arrow. Compressed arrows make the graphic feel anxious.

**Diagonal arrows from a diamond:**
- Start the line at the diamond's corner point
- End 6px above the destination box's top-left or top-right corner
- Place the arrowhead so the tip lands at the box edge

---

## Diagram Type Reference

### Decision Flow Diagram

Structure: top box → arrow → process box → arrow → diamond → 4 arrows → 4 option boxes

**Minimum canvas height: 620px.**

Layout proportions:
- Top box: y=20
- Process box: y=136 (gap of ~42px after top box + arrow)
- Diamond center: y=330 (gap of ~82px after process box + arrow)
- Side boxes (left/right): vertically centered on diamond center (diamond_center_y - box_height/2)
- Bottom boxes: y = diamond_center_y + 170 (enough clearance for diagonal arrows)

### Comparison Table

Structure: header row + alternating data rows + vertical column dividers

Row height: minimum 76px. If any cell has 3+ lines, use 90px.
Column widths proportional to content — name column narrower, data columns wider.
Always draw a full-width background `<rect>` for each row before text.

### 2x2 Grid / Four Archetypes

Card minimum width: 185px. Card minimum height: 220px.
Gap between cards: 10–15px.
Each card: title at y+24, divider line at y+38, body text starting at y+60.

---

## Pre-Render Checklist

Before calling `cairosvg.svg2png`:

- [ ] viewBox height ≥ bottom of lowest element + 20px margin
- [ ] viewBox width ≥ right edge of rightmost element + 10px margin
- [ ] No text within 8px of its parent box edge
- [ ] All font sizes ≥ 10px
- [ ] All text on colored backgrounds uses white or tinted fill (never #000 or #444)
- [ ] Arrowheads are `<polygon>`, not `<marker>`
- [ ] Scale ≥ 2.0 (prefer 2.5)
- [ ] `&` escaped as `&amp;` in SVG string
- [ ] PNG saved to same folder as HTML post file
- [ ] HTML post uses `<img>` tag, not inline `<svg>`

After rendering, **view the PNG using the Read tool** to visually verify:
- All boxes fully visible including bottom border
- Text centered within boxes
- Arrows land cleanly on box edges
- No clipped content at any edge
- Proportions feel balanced

If anything looks off, fix the SVG and re-render before saving.

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| viewBox set before content height calculated | Always tally height from elements first |
| Text lines 14px apart | Use 16–18px line spacing minimum |
| Using `<marker>` for arrowheads | Use `<polygon>` only |
| Text split across lines using SVG auto-wrap | Manual `<text>` elements for each line |
| Forgetting to escape `&` | Use `&amp;` everywhere in SVG |
| Black text on colored boxes | Use `#ffffff` or tinted white |
| Not viewing the PNG before saving | Always read it back and inspect visually |
