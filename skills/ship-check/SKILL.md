---
name: ship-check
description: Combined pre-merge gate — static code review + Puppeteer UI test run. Posts unified findings to a GitHub PR comment and reports in chat. Usage: /ship-check [PR#] [test-plan-path] [app-url]
version: 1.0
---

## Proactive trigger

Offer to run `/ship-check` (do not run automatically — ask first) when the user says anything that matches these patterns:

**Bug complaints:** "this isn't working", "something's broken", "I keep getting errors", "it's still failing", "nothing is working", "why is this broken", "this is a mess", "keeps crashing", "can't get it to work", "it broke again"

**Code quality frustration:** "this code is terrible", "what a disaster", "this is wrong", "I hate this", "this makes no sense", "why did I write this", "this is garbage", "total mess"

**Stuck signals:** "I've been fighting this for hours", "I can't figure out why", "I give up", "I'm losing my mind", "spent all day on this", "still not fixed", "going in circles"

When you detect these, say: "Want me to run `/ship-check` against this? It'll do a static code review + live UI test run and tell you exactly what's broken." Then wait for a yes/no before doing anything.

Only offer once per conversation on the same topic. Don't offer if the user has just finished a `/ship-check` run.

## Purpose

Run a two-phase quality gate before merging a branch:
1. **Code review** — static analysis with multiple agents (bugs, CLAUDE.md compliance, git history context)
2. **UI test run** — Puppeteer smoke tests against the live app using a test plan file

Combine findings into a single report. Post to GitHub PR if PR# is given; always report in chat.

## Input parsing

Args: `/ship-check [PR#] [test-plan-path] [app-url]`

- `PR#` — integer GitHub PR number (optional; omit for chat-only output)
- `test-plan-path` — path to a markdown file containing test cases (optional; skip UI tests if absent)
- `app-url` — live app URL to test against (optional; required if test-plan-path is given)

If no args: run only the code review against the current branch diff (no PR comment, no UI tests).

## Phase 1: Code review

### 1a. Eligibility check (Haiku agent)
Verify the PR/branch is eligible: not closed, not a draft, not a trivial automated change, no prior ship-check comment. If ineligible, stop and say why.

### 1b. Find CLAUDE.md files (Haiku agent)
Return a list of CLAUDE.md file paths relevant to the changed files — root CLAUDE.md plus any in modified directories.

### 1c. PR summary (Haiku agent)
Read the PR diff and return a 3-5 sentence summary of what changed and why.

### 1d. Parallel review agents (5 Sonnet agents, run in parallel)
Each agent reads the diff and returns a list of issues with confidence scores:

- **Agent 1 — CLAUDE.md compliance:** Do the changes comply with all relevant CLAUDE.md rules? Flag violations with the specific rule quoted.
- **Agent 2 — Bug scan:** Shallow scan for obvious bugs in the changed lines only. Large bugs only — no nitpicks. No linter-catchable issues.
- **Agent 3 — Git history context:** Read git blame and recent history for the modified files. Flag issues that only make sense given the history (e.g., a pattern that was intentionally removed being re-introduced).
- **Agent 4 — Prior PR comments:** Read previous PRs that touched these files. Flag if any prior reviewer comment applies to the current change.
- **Agent 5 — Code comment compliance:** Read code comments in modified files. Flag if the changes violate documented invariants or workarounds.

### 1e. Confidence scoring (parallel Haiku agents, one per issue)
Score each issue 0–100:
- 0: False positive or pre-existing issue
- 25: Might be real but unverified
- 50: Real but minor / rare in practice
- 75: Verified, likely to be hit, important
- 100: Confirmed, will happen frequently

**Filter: keep only issues scoring ≥ 80.**

## Phase 2: UI tests (skip if no test-plan-path given)

### 2a. Load test plan
Read the test plan file. Extract individual test cases — each has: a name/number, interaction steps, and an expected outcome.

### 2b. Execute tests with Puppeteer MCP
For each test case in order:

1. Navigate to the relevant URL using `mcp__puppeteer__puppeteer_navigate`
2. Execute interaction steps:
   - Clicks: use `mcp__puppeteer__puppeteer_click` with a CSS selector (not evaluate btn.click — HTMX won't fire)
   - Fill inputs: use `mcp__puppeteer__puppeteer_fill`
   - HTMX requests: use `mcp__puppeteer__puppeteer_evaluate` with `htmx.ajax(...)` wrapped in an IIFE
   - Read DOM state: use `mcp__puppeteer__puppeteer_evaluate`
   - Visual checks: use `mcp__puppeteer__puppeteer_screenshot`
3. Verify expected outcome
4. Record: PASS / FAIL / SKIP with a one-line reason

**Puppeteer rules:**
- Always wrap evaluate() JS in IIFEs: `(function() { const x = ...; })()` — evaluate calls share a JS context; re-declared variables error
- HttpOnly cookies are invisible to fetch() in evaluate(); use `puppeteer_click` for auth-required actions
- `hx-swap="none"` routes require client-side DOM updates for visual feedback — verify the CSS class, not a server response
- `overflow-x:auto` containers clip `position:absolute` children — if a popover is invisible, check for overflow clipping
- After any HTMX `outerHTML` swap, re-query elements — old references are detached

### 2c. On FAIL: attempt fix
If a test fails and the root cause is clearly in the codebase (not a test setup issue):
1. Read the relevant source file(s)
2. Identify and apply the fix
3. Redeploy (use the project's deploy command from CLAUDE.md)
4. Re-run that test case
5. Record the fix in the report

## Phase 3: Combined report

### Format

```
## Ship check — [branch or PR title]

### Code review
[N issues found / No issues found]

[For each issue:]
1. **[Brief description]** — [why it matters]
   [Link to file + line range with full SHA]

### UI tests ([passed]/[total] passed)
| # | Test | Result | Note |
|---|------|--------|------|
| 1 | [name] | PASS | |
| 2 | [name] | FAIL | [what broke + fix applied] |
| 3 | [name] | SKIP | [why] |

### Verdict
[READY TO MERGE / NEEDS FIXES / BLOCKED]

- Ready to merge: no code review issues ≥80, all UI tests PASS or SKIP
- Needs fixes: issues found, fixes applied this session — re-run to confirm
- Blocked: unfixed issues remain

🤖 Generated with [Claude Code](https://claude.ai/code)
```

### Output targets
- **Chat:** Always post the full report inline
- **GitHub PR comment:** Post via `gh pr comment [PR#] --body "..."` if PR# was given

## What this skill does NOT do
- Run builds, linters, or type checkers (CI handles those)
- Fix security issues (escalate to user)
- Auto-merge
- Run tests that require credentials not already in the browser session
