---
name: weekly-ai-chat-review
description: Weekly Monday review of open AI chats to identify which ones need Todoist follow-up tasks
---

It's your weekly AI chat review. Do NOT ask Jeff to scan anything himself — you do the review first, then present findings for his quick yes/no decisions.

## Step 1: Pull conversation history from Claude.ai

Use the Chrome browser tool to navigate to claude.ai. Open the sidebar and use the Claude.ai API to retrieve conversations from the past 10 days (not just 7 — use 10 days to catch anything from the previous week that might have slipped). Use this API call pattern to get timestamps:

- Get org ID from: GET /api/account (field: memberships[0].organization.uuid)
- Get conversations: GET /api/organizations/{orgId}/chat_conversations?limit=50&offset=0
- Filter to conversations with updated_at or created_at within the past 10 days

## Step 2: Classify each conversation

For each conversation from the past 10 days, classify it as one of:

**Likely open** — flag these for Jeff's attention:
- Ran for more than one back-and-forth exchange (updated_at is significantly later than created_at)
- Title suggests ongoing work: performance reviews, proposals, analysis, strategy, planning, mapping, synthesis, tracking, review, guidelines, frameworks
- Created more than 24 hours ago but still recently updated (suggests multiple sessions)
- Topic is inherently iterative (e.g. flight research without a clear booking, document drafts, team management decisions)

**Probably done** — mention briefly but don't push:
- Quick factual Q&A (salary lookups, how-to questions, definitions, cost estimates)
- Single-exchange conversations (created_at ≈ updated_at within a few minutes)
- Titles like "how X works", "cost of X", "what is X", "salary in X"

## Step 3: Present the findings

Lead with the "Likely open" list. For each item include:
- Conversation title
- Date range (created → last updated)
- One-sentence assessment of why it might still be open

Then show the "Probably done" list more briefly — just titles and dates, no need to elaborate.

Do NOT ask Jeff to go look at the conversations himself. You've already done the first pass.

## Step 4: For each item Jeff confirms as open

Ask only: "What needs to happen next — do you need to do something, or does Claude need to do something?"

Then draft a crisp Todoist task in this format:
"[Action verb] + [topic]"

Examples:
- "Continue value stream mapping exercise with Claude"
- "Finish reviewing AI tool recommendations"
- "Follow up on flight options from Las Palmas to California"

## Step 5: Final check

Ask: "Any chats you started this past week that aren't showing up here — things you know are unfinished?"

Then present the complete task list, formatted cleanly for direct paste into Todoist.

## Ground rules

- Keep the session to 5–10 minutes.
- If Jeff is uncertain about an item, default to skipping it.
- Do not create tasks for everything — the goal is a short, high-signal list.
- You do the legwork. Jeff just makes the calls.