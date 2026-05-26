---
name: publish-to-wordpress
description: "Publish or update posts on jeffreybeaumont.com using the WordPress MCP connector. Use this skill whenever Jeff wants to publish, create a draft, update an existing post, or fetch an existing post for rewriting. Triggers include: \"publish this post,\" \"create a WordPress draft,\" \"push this to WordPress,\" \"update my post,\" \"fetch post [slug],\" \"create a draft on my site,\" or any request to interact with jeffreybeaumont.com content via MCP. Always run draft-blog-post and public-writing before this skill unless fetching an existing post for review."
---

# Jeff Beaumont · Publish to WordPress Skill
### Version 1.1 — May 2026

## Skill position in the workflow

```
draft-blog-post → public-writing → [THIS SKILL: publish-to-wordpress]
```

---

## Platform details

- **Host:** WordPress.com (not self-hosted — no SSH/SFTP access)
- **Site identifier for MCP:** `jeffreybeaumont.com`
- **MCP tool:** `wpcom-mcp-content-authoring`
- **Dashboard:** https://wordpress.com/home/jeffreybeaumont.com

Note: The site does NOT appear in `wpcom-mcp-user-sites` results (custom domain
mapping issue), but IS accessible directly via the content-authoring tool using
`jeffreybeaumont.com` as the `wpcom_site` value.

---

## Enabled MCP operations

| Operation | Use |
|---|---|
| `posts.create` | Create new posts as drafts |
| `posts.update` | Update existing posts and drafts |
| `posts.list` / `posts.get` | Read posts by ID or slug |
| `tags.list` / `categories.list` | Read taxonomy |

**Disabled — do not attempt:**
`posts.delete`, `tags.create`, `tags.delete`, `categories.create`,
`categories.delete`, `comments.*`, `media.update`, `media.delete`

---

## Content format

Pass the full post content as an **HTML string** in the `content` field.

- Plain HTML only — do not use Gutenberg block syntax
- No `<html>`, `<head>`, or `<body>` wrapper tags
- WordPress.com handles the HTML correctly
- The block editor can still be used for manual edits afterward

---

## Default publish workflow

**Step 1: Always create as `status: "draft"`** unless Jeff explicitly says
"publish." Never set `status: "publish"` without a direct instruction. Drafts
are reviewed before going live.

**Step 2: Check `_content_warnings`** in the response after every create or
update. WordPress silently strips certain HTML elements (custom attributes,
unsupported tags, inline styles). If warnings appear, report them to Jeff and
simplify the markup before retrying.

**Step 3: After a successful create or update, share both links:**
- Edit: `https://wordpress.com/post/jeffreybeaumont.com/{post_id}`
- Preview: `https://jeffreybeaumont.com/?p={post_id}&preview=true`

---

## Categories

Only one category exists: **Uncategorized (ID: 1)**. Use it for all posts.
Do not attempt to create new categories.

---

## Tags — pass as integer IDs

Tags must be passed as an array of integer IDs. Select 3–5 per post. Do not
attempt to create new tags.

**GTM / CS Ops / AI posts:**

| Tag | ID |
|---|---|
| Leadership | 7885 |
| Customer Success | 655919 |
| SaaS | 17927 |
| Artificial Intelligence | 12374 |
| ai | 14067 |
| Business | 179 |
| Strategy | 8553 |
| Customer Experience | 88195 |
| Technology | 6 |
| Productivity | 2704 |
| Startup | 4621 |
| Management | 4236 |

**Personal / narrative posts:**

| Tag | ID |
|---|---|
| Life Lessons | 2501 |
| Life | 124 |
| Leadership | 7885 |
| Humility | 104576 |
| Nonprofit | 6917 |

---

## Images

1. Constrain the image width — add a width attribute or inline style so WordPress doesn't render it at full resolution. A max-width of 800px or 100% works well for this theme.
2. Make it clickable to enlarge — wrap the <img> in an <a> tag pointing to the full-size image URL.

---

## Standard posts.create call

```json
{
  "wpcom_site": "jeffreybeaumont.com",
  "operation": "posts.create",
  "status": "draft",
  "title": "Post title here",
  "content": "<p>Full HTML content...</p>",
  "categories": [1],
  "tags": [7885, 655919, 17927]
}
```

---

## Fetching existing posts for rewrite

To get the current content of a post before rewriting:

```
posts.get with:
  wpcom_site: "jeffreybeaumont.com"
  slug: "the-post-slug"   (or id: 12345)
  context: "edit"
  include_fields: ["id", "title", "content", "slug", "status", "tags"]
```

The slug is the URL path segment. For example:
`jeffreybeaumont.com/2026/04/15/nobody-defined-done/` → slug is `nobody-defined-done`

---

## Updating an existing post

```json
{
  "wpcom_site": "jeffreybeaumont.com",
  "operation": "posts.update",
  "id": 12345,
  "content": "<p>Updated HTML content...</p>",
  "status": "draft"
}
```

Always update to `draft` status first unless Jeff says to publish directly.

---

## Batch rewrite workflow (30+ post backlog)

For each post in the rewrite backlog:

1. Fetch the original via `posts.get` using the slug
2. Run the public-writing skill (Sharpen or Recontextualize mode)
3. Create a new draft via `posts.create` (or update the existing post via
   `posts.update` if replacing in place)
4. Check `_content_warnings`
5. Share edit + preview links with Jeff
6. Jeff reviews and publishes — do not publish without confirmation

---

## Pre-publish confirmation

Before any `posts.create` or `posts.update` call, state in one line what will
happen and wait for approval:

> "Creating a draft titled '[Title]' on jeffreybeaumont.com with tags
> [Leadership, SaaS, Strategy]. Go?"

Do not fire the MCP call without a "go" or explicit approval.