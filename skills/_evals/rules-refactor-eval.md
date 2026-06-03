# Rules Refactor Evaluation — Skill Trigger Fidelity

This document verifies that extracted guardrails load correctly and that non-code sessions do not pollute with engineering skills.

**Test model:** Each scenario begins with a user message. Verify:
1. Correct skills load (listed under "Should load")
2. No unwanted coding skills load (listed under "Should NOT load")
3. Progressive disclosure works — guardrails fire only when relevant

---

## Code-Heavy Sessions

These scenarios should load engineering convention skills.

### Scenario 1: Modal Deploy

**Prompt:** "I'm deploying the recruiting-engine app to Modal. Want to walk through the safety checks before I push?"

**Should load:**
- `modal-conventions` (Modal deployment safety, worktree checks, PR workflow)

**Should NOT load:**
- `database-conventions`, `agent-delegation`, `cloudflare-stack-rules`

**Expected behavior:** Modal guardrails fire: worktree location check, concurrent session coordination, PR requirement for app/ changes surface immediately.

---

### Scenario 2: SQLite Migration

**Prompt:** "Adding a new column to the investments DB. The table has 10M rows. How do I avoid downtime?"

**Should load:**
- `database-conventions` (SQLite migration pattern, idempotent schema, Modal Volume persistence)

**Should NOT load:**
- `modal-conventions` (unless mentioned alongside persistence), `cloudflare-stack-rules`

**Expected behavior:** SQLite migration rules fire: narrow except pattern, idempotent ALTER TABLE, WAL artifacts in gitignore.

---

### Scenario 3: Web Scraping with Bot Detection

**Prompt:** "SeekingAlpha is 403-blocking my scraper. I need to fetch current analyst ratings for 50 tickers. What's the fallback chain?"

**Should load:**
- `scraping-conventions` (API-first, curl_cffi TLS impersonation, Jina → Firecrawl → BeautifulSoup chain, content caps)

**Should NOT load:**
- `gemini-api-conventions`, `agent-delegation`

**Expected behavior:** Scraping rules fire: never bare BeautifulSoup for major boards, use curl_cffi for TLS-protected targets, cap at 30k chars before LLM input.

---

### Scenario 4: Delegating to Antigravity CLI

**Prompt:** "I need you to refactor the recruiting-engine main.py with Antigravity. How should I set it up?"

**Should load:**
- `agent-delegation` (two-stage workflow, Stage 1 plan/Stage 2 execute, git safety branch, quota failover)
- `modal-conventions` (app/ files require PR, deploy coordination)

**Should NOT load:**
- `database-conventions`, `frontend-htmx-conventions`

**Expected behavior:** Delegation rules fire: Stage 1 no-execute gate, `--dangerously-skip-permissions` only after approval, create safety branch before Stage 2, `git diff` review required.

---

### Scenario 5: HTMX Route Redirect Bug

**Prompt:** "I have a form handler that returns RedirectResponse after a POST. But when HTMX calls it, the page breaks. Full HTML swaps into the div instead of just the fragment."

**Should load:**
- `frontend-htmx-conventions` (HTMX redirect trap, HX-Request header branching, partial endpoint URLs in fragments)

**Should NOT load:**
- `modal-conventions`, `database-conventions`

**Expected behavior:** HTMX rules fire: never return RedirectResponse to HTMX requests, branch on HX-Request header, return HTML fragment not full page to HTMX callers.

---

### Scenario 6: Gemini API Rate Limit

**Prompt:** "I'm hitting 429 errors on my Gemini calls in recruiting-engine. The pacing is correct (5s sleep), but it keeps 429'ing. What's happening?"

**Should load:**
- `gemini-api-conventions` (rate-limit diagnosis, exponential backoff, limit:0 probe, free-tier quota behavior)

**Should NOT load:**
- `agent-delegation`, `scraping-conventions`

**Expected behavior:** Gemini rules fire: verify backoff ladder (15,30,60,120,240s), write probe_model_quota function to test each model, limit:0 is account-level (billing required), not code-fixable.

---

## Writing/Ops Sessions (Zero Code Skills)

These scenarios should NOT load any coding convention skills. This tests that progressive disclosure doesn't over-load operational contexts.

### Scenario 7: Friday CoS Review

**Prompt:** "Running the Friday Chief of Staff review. Let me start with my Todoist snapshot and Notes."

**Should load:**
- ZERO coding convention skills (cowork plugins may trigger, but no engineering rules)

**Should NOT load:**
- `modal-conventions`, `database-conventions`, `python-style`, any engineering skills

**Expected behavior:** Silent load — no guardrails fire. CoS skills and operational context drive the session.

---

### Scenario 8: Draft Blog Post

**Prompt:** "I want to draft a post for jeffreybeaumont.com about operational systems thinking. Send it to my drafts."

**Should load:**
- ZERO coding convention skills

**Should NOT load:**
- `python-style`, `cloudflare-stack-rules`, `agent-delegation`

**Expected behavior:** Writing skills fire (public-writing, draft-blog-post), engineering rules silent. Brand voice and messaging tone dominate.

---

### Scenario 9: Mercy Ships Finance Email

**Prompt:** "Writing the Friday Happy Friday email to the team. It's a summary of this week's cash position and coming payables."

**Should load:**
- ZERO coding convention skills

**Should NOT load:**
- Any engineering skills

**Expected behavior:** Internal-writing skill fires (if present), engineering guardrails silent. Finance narrative and leadership tone drive the session.

---

### Scenario 10: Todoist Task Review

**Prompt:** "Let me review my Todoist backlog for the week. Anything stalled?"

**Should load:**
- ZERO coding convention skills

**Should NOT load:**
- Any engineering skills

**Expected behavior:** Todoist tools fire (if any), engineering rules silent. Task state and status tracking dominate.

---

## Mixed Sessions (Partial Load)

These scenarios verify selective loading — analyzing code without intent to deploy.

### Scenario 11: Code Review (No Deploy)

**Prompt:** "Review this recruiting-engine app/main.py logic. Does the rate-limit backoff look correct? Don't make changes yet."

**Should load:**
- `gemini-api-conventions` (rate-limit, backoff ladder, error handling)
- OPTIONAL: `python-style` (code review context)

**Should NOT load:**
- `modal-conventions` (not deploying), `agent-delegation` (not delegating), `cloudflare-stack-rules` (not a Worker)

**Expected behavior:** Gemini API rules available for inline review, but deploy/delegation guardrails silent since there's no intent to execute.

---

### Scenario 12: Architecture Analysis

**Prompt:** "I'm analyzing whether recruiting-engine should move from SQLite to Supabase. Database size is 2GB, single-user access. What's the trade-off?"

**Should load:**
- `database-conventions` (SQLite vs Supabase comparison, use-case factors, Jeff's account availability)

**Should NOT load:**
- `python-style`, `modal-conventions` (not yet building), `agent-delegation`

**Expected behavior:** Database rules fire with comparative guidance, but implementation guardrails (migration patterns, schema safety) remain passive until code-writing phase.

---

## Guardrail Coverage Check

Verify each guarda rail-bearing skill has at least one scenario that tests it:

| Skill | Scenario | Guardrail tested |
|-------|----------|------------------|
| `modal-conventions` | 1, 4 | Deploy safety, worktree check, PR requirement |
| `database-conventions` | 2, 12 | SQLite migrations, idempotent schema, Supabase choice gate |
| `scraping-conventions` | 3 | API-first, curl_cffi, Firecrawl chain, content caps |
| `agent-delegation` | 4 | Two-stage gate, --dangerously-skip-permissions, git safety branch |
| `frontend-htmx-conventions` | 5 | HTMX redirect trap, HX-Request branching |
| `gemini-api-conventions` | 6 | Rate-limit backoff, probe_model_quota, limit:0 diagnosis |
| `python-style` | 5, 11 (optional) | Style, f-string traps, Jinja2 truthiness |
| `cloudflare-stack-rules` | Not tested | (No active Cloudflare sessions yet) |
| `notion-conventions` | Not tested | (Notion writes usually low-risk, embedded in other flows) |

**Coverage:** 6/9 core skills explicitly tested. `cloudflare-stack-rules` and `notion-conventions` are lower-frequency and may not appear in this round.

---

## Pass Criteria

A skill passes if:
1. It loads when expected (Scenario matches trigger description)
2. It does NOT load when listed "Should NOT load"
3. Guardrails in the skill are complete and verbatim from original AI_RULES

## Failure Modes to Watch

- **Over-loading:** Coding skills load in writing sessions (Scenario 7-10 violations)
- **Under-loading:** Coding skills do NOT load when the domain is active (Scenario 1-6 violations)
- **Silent guardrails:** Rules exist in skill but don't surface in chat output during execution
- **Drift:** Skill guardrails are paraphrased or weakened versions of originals (rule-count drop)

---

## Next Steps

After first production use:
1. Monitor for guardrail silence (watch logs for missed gates)
2. Track false positives (skills loading in wrong contexts)
3. Adjust trigger descriptions if needed to improve fidelity
4. If any guardrail was softened in extraction, restore to exact original text
