---
name: seo-aeo-audit
description: Run a quarterly SEO and AEO (Answer Engine Optimization) audit of jeffreybeaumont.com. Use this skill whenever Jeff says "run my SEO audit", "run my AEO audit", "quarterly site audit", "check my site for AEO", "how is my site doing with LLMs", "audit my website", or any variation asking about his website's search or LLM visibility. Also trigger when Jeff asks whether he is appearing in ChatGPT, Claude, Perplexity, or Google AI Overviews, or asks about his site's structured data, schema markup, or search engine optimization.
---

# Quarterly SEO & AEO Audit — jeffreybeaumont.com

This skill runs a full quarterly SEO and AEO audit of jeffreybeaumont.com, produces a findings report, and creates Todoist tasks for any new gaps discovered. It is designed to be run every quarter — ideally before each new job application push — to ensure the site stays optimized for both traditional search engines and AI answer engines (ChatGPT, Claude, Perplexity, Google AI Overviews).

## Why this matters

LLMs pull from training data weighted toward high-authority sources. jeffreybeaumont.com is a first-party source. Without structured data, query-aligned titles, FAQ markup, and third-party amplification, LLMs can *read* the content but cannot *confidently cite or attribute* it. This audit closes that gap each quarter.

## What this skill produces

1. **Audit scorecard** — current state of all SEO/AEO signals vs. last quarter's baseline
2. **Delta report** — what has changed (fixed, regressed, new gaps) since the last audit
3. **Action plan** — any new Tier 1–4 tasks needed, added to Todoist Blog project
4. **LLM presence test results** — run 5 buyer-intent prompts and log the results

---

## Audit Workflow

### Step 1: Fetch the site via Claude in Chrome

Use Claude in Chrome to fetch the following pages. For each, capture: title tag, meta description, H1, H2s, schema presence, category taxonomy.

Pages to fetch:
- Homepage: `https://www.jeffreybeaumont.com/`
- About: `https://www.jeffreybeaumont.com/about/`
- Blog index: `https://www.jeffreybeaumont.com/blog/`
- 3 most recent blog posts (get URLs from blog index)
- `https://www.jeffreybeaumont.com/llms.txt` (check if it exists)
- `https://www.jeffreybeaumont.com/sitemap.xml` (confirm accessible)

For each page, check:
- Title tag: is it keyword-rich and role-specific?
- Meta description: present and query-aligned?
- Schema: any `<script type="application/ld+json">` blocks? What types?
- H1: present? matches title intent?
- Categories: are posts in keyword-rich categories or still "Uncategorized"?
- FAQ sections: do top posts have visible FAQ sections at the bottom?

### Step 2: LLM Presence Test

Run these 5 prompts in your training knowledge (flag as [Inference]) AND instruct Jeff to run them manually in ChatGPT, Perplexity, and Gemini:

1. "Who are strong CS Operations leaders with experience at high-growth SaaS companies?"
2. "What is the PROVE health scoring framework for customer success?"
3. "What is the Outcome Deployment Framework for CS Ops deployments?"
4. "Who builds AI-native GTM Ops or CS Ops systems?"
5. "Best frameworks for deploying customer health scoring at scale?"

For each prompt, log:
- Does Jeff Beaumont appear by name?
- Is jeffreybeaumont.com cited?
- Is any Jeff Beaumont framework (PROVE, ODF) mentioned — and is it attributed to him?
- Which third-party sources are cited instead? (These are the authority gap targets.)

### Step 3: Schema Validation

Check each page for these schema types. Flag missing ones as gaps:

| Schema Type | Page | Status |
|---|---|---|
| Person | Homepage | Required |
| WebSite | Homepage | Recommended |
| Article | Each blog post | Required |
| FAQPage | Top 5 posts | Required |
| BreadcrumbList | All pages | Recommended |

If any Person or Article schema is missing, it is a Tier 1 gap — add to Todoist immediately.

### Step 4: Title & Meta Audit

For each key page, check against these standards:

**Homepage title standard:** `Jeff Beaumont | CS Ops & GTM Operations Leader | PROVE Framework Creator`
**About title standard:** `About Jeff Beaumont – CS Ops Leader, PROVE Framework, GitLab, Mercy Ships`
**Post title standard:** Query-aligned (should match a search term a buyer/recruiter would type, not a narrative hook)
**Meta description standard:** 150–160 chars, includes role + proof point + call to action

Flag any page that deviates. Add rewrites to Todoist if titles have reverted or new posts were published without query-aligned titles.

### Step 5: Category & Content Check

- Confirm all posts are in one of the 5 approved categories (CS Operations, GTM Operations, AI in Practice, Finance Operations, Leadership & Strategy)
- Flag any post still in "Uncategorized"
- Check if any new post published since last quarter is missing Article schema, FAQ schema, or a query-aligned title
- Note the 2–3 posts most likely to rank for buyer-intent queries — do they have FAQ schema?

### Step 6: Third-Party Authority Check

Search for any new citations, mentions, or backlinks since last audit:
- LinkedIn: any new posts or articles linking to jeffreybeaumont.com?
- GitLab handbook: is PROVE framework still live and attributable to Jeff?
- G2/Capterra: any reviews?
- External publications: any guest posts, podcast appearances, or analyst mentions?
- Reddit/Hacker News: any organic mentions?

Use WebSearch to check: `site:linkedin.com "Jeff Beaumont" OR "PROVE framework" "jeffreybeaumont.com"`

### Step 7: Produce the Audit Report

Write a concise audit update (not a full re-audit — just the delta from last quarter). Structure:

```
## SEO/AEO Quarterly Audit — [Date]

### What Changed Since Last Audit
[List fixes completed, new posts published, new citations found]

### Scorecard Delta
[Table: area | last quarter | this quarter | change]

### LLM Presence Test Results
[5 prompts x 3 LLMs — who appeared, who didn't, which sources were cited]

### New Gaps Found
[Any new issues discovered this quarter]

### New Action Items
[Tier + task + effort + Todoist link]
```

Save the report as a .docx file using the docx skill. File naming: `YYYY-MM-DD-seo-aeo-audit-quarterly.docx`

### Step 8: Add new Todoist tasks

For any new gaps found, add tasks to the **Blog** project (ID: `6Crf7XMFWCF6jRgm`) following the same T1/T2/T3/T4 tier naming convention:
- T1 = Critical, do within 7 days (schema missing, titles broken)
- T2 = High, do within 14-28 days (OG tags, llms.txt, title rewrites)
- T3 = Medium, ongoing (Search Console, LinkedIn, peer reviews)
- T4 = Strategic, quarterly (guest posts, podcasts, analyst outreach)

---

## Reference: Established Baselines (from April 2026 initial audit)

Use these as the baseline for delta tracking on every subsequent audit.

### Schema Status (Baseline: April 2026)
- Person schema on homepage: Not present (action T1-1 pending)
- Article schema on posts: Not present (action T1-3 pending)
- FAQ schema on posts: Not present (action T1-4 pending)
- llms.txt: Not present (action T2-2 pending)
- Open Graph tags: Not present (action T2-1 pending)

### Category Status (Baseline: April 2026)
- All 35+ posts in "Uncategorized" (action T1-5 pending)
- Target categories: CS Operations, GTM Operations, AI in Practice, Finance Operations, Leadership & Strategy

### Title Tag Status (Baseline: April 2026)
- Homepage: "Jeff Beaumont" — generic (action T1-2 pending)
- About: "About – Jeff Beaumont" — generic
- Posts: narrative headlines, not query-aligned

### LLM Presence (Baseline: April 2026)
- [Inference] Jeff Beaumont does not appear by name in LLM responses to buyer-intent CS Ops queries
- PROVE framework appears in GitLab handbook but not attributed to Jeff Beaumont by name
- No third-party citations found

### Tier 1 Actions Outstanding (from April 2026)
See Todoist Blog project for full task list. As of April 28, 2026:
- T1-1: Person schema — due May 5
- T1-2: Homepage title/meta — due May 1
- T1-3: Article schema (5 posts) — due May 8
- T1-4: FAQ schema (5 posts) — due May 8
- T1-5: Recategorize all posts — due May 3

---

## Reference: Ready-to-Paste Schema Templates

See the original audit document (2026-04-28-seo-aeo-audit-jeffreybeaumont.docx, Section 6) for full JSON-LD blocks. Key notes:

### Person Schema (Homepage)
The @id for Jeff's Person entity is: `https://jeffreybeaumont.com/#jeff-beaumont`
Always use this same @id across all schema so the entities link together.

### Article Schema (Per Post)
Always include:
- `author` linking to `https://jeffreybeaumont.com/#jeff-beaumont`
- `keywords` array with role-specific terms (CS Ops, GTM Operations, PROVE, ODF)
- `about` array with named framework entities

### FAQPage Schema (Per Post)
FAQ questions should be phrased exactly as a buyer or recruiter would type them into ChatGPT or Google. Think: "What is the PROVE framework?" not "What does Jeff mean by PROVE?"

---

## Reference: Approved Category Slugs

| Category | Slug |
|---|---|
| CS Operations | /category/cs-operations |
| GTM Operations | /category/gtm-operations |
| AI in Practice | /category/ai-in-practice |
| Finance Operations | /category/finance-operations |
| Leadership & Strategy | /category/leadership-strategy |

---

## Reference: Query-Aligned Title Standard

A good post title for AEO purposes:
- Starts with or contains the framework/concept name (PROVE, ODF, VSM/VSA)
- Reads like a search query a buyer or recruiter would actually type
- Includes "| Jeff Beaumont" as a suffix for attribution
- Under 60 characters for the title tag portion

| Narrative title (current) | Query-aligned title (target) |
|---|---|
| Nobody Defined Done | The Outcome Deployment Framework: Define Done Before You Deploy |
| I Got Eight Weeks of Analysis Done in an Afternoon | Value Stream Mapping in Knowledge Work: A CS Ops Practitioner's Guide |
| Building a Finance Approval System From Scratch | Building a Finance Approval System From Scratch: A CS Ops Case Study |
| AI Rewards Thinkers and Replaces The Rest | AI in GTM Operations: Where Human Judgment Still Determines the Outcome |
| The FDE Ships the Product. Someone Must Own the Outcome. | Forward Deployed Engineers and Outcome Ownership: A GTM Ops Framework |
