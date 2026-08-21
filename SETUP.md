# Setup Guide — Claude + Antigravity CLI Multi-Agent System

This guide shows you how to replicate a multi-agent AI workflow where **Claude Code (Haiku) orchestrates** and **Antigravity CLI (`agy`) handles the heavy lifting** as subagents. Claude stays lightweight and in control; Gemini's large context window does the reading, writing, and analysis.

Designed to be readable by both humans and AI agents — you (or your AI) can follow these steps to replicate the full setup.

---

## What You're Building

```
You
 └── Claude Code (Haiku model — orchestrator)
      ├── @gemini-researcher  — codebase analysis, QA, validation
      ├── @gemini-builder     — implementation, code changes
      ├── @gemini-analyst     — research, competitive analysis, documents
      ├── @gemini-security-reviewer  — security audits before deployment
      └── @outcome-architect  — goal definition, project planning gate
```

Claude Haiku keeps context small and cost low. The Gemini agents handle large context reads (entire codebases, big docs, multi-file analysis). Each agent has a defined role and escalation path — Claude routes work to the right specialist rather than doing everything inline.

---

## Prerequisites

| Tool | What it is | Where to get it |
|------|-----------|-----------------|
| **Claude Code** | Anthropic's CLI for Claude | `npm install -g @anthropic-ai/claude-code` — requires an Anthropic account |
| **Antigravity CLI** (`agy`) | Google's Gemini-powered CLI (replaced Gemini CLI June 2026) | `pip install antigravity-cli` or see [antigravity.dev](https://antigravity.dev) |
| **Git** | Version control | Pre-installed on macOS; `apt install git` on Linux |
| **A project to work in** | Any git repo | `git init myproject && cd myproject` |

**Accounts needed:**
- Anthropic account (for Claude Code) — Claude Pro or Team plan recommended for heavy use
- Google account (for `agy` / Antigravity CLI)

---

## Installation

### 1. Install Claude Code

```bash
npm install -g @anthropic-ai/claude-code
claude  # follow the login prompt
```

### 2. Install Antigravity CLI

```bash
pip install antigravity-cli
agy --version  # verify
```

Note the binary path — you'll need it. Typically:
- macOS (pip): `~/.local/bin/agy`
- macOS (pipx): `~/.local/bin/agy`
- Linux: `~/.local/bin/agy`

Confirm: `which agy`

### 3. Configure Claude Code to use Haiku

When you start Claude Code, select the Haiku model. In the CLI:

```bash
claude --model claude-haiku-4-5-20251001
```

Or set it as your default in `~/.claude/settings.json`:

```json
{
  "model": "claude-haiku-4-5-20251001"
}
```

Haiku is the right choice for the orchestrator role: fast, cheap, and capable enough to route tasks, read short files, and manage workflow. The Gemini agents handle the large-context heavy lifting.

### 4. Install the agents

Copy the agent definition files into your Claude Code agents directory:

```bash
# From this repo
cp agents/*.md ~/.claude/agents/

# Verify
ls ~/.claude/agents/
```

You should see: `gemini-researcher.md`, `gemini-builder.md`, `gemini-analyst.md`, `gemini-security-reviewer.md`, `outcome-architect.md`

### 5. Update the binary path in each agent file

Open each agent `.md` file and replace the binary path with your actual `agy` location:

```bash
# Find your agy path
which agy

# Replace the path in all agent files (example — adjust to your actual path)
sed -i '' 's|\[YOUR_AGY_PATH\]|/your/actual/path/to/agy|g' ~/.claude/agents/*.md ~/.claude/skills/agent-delegation/SKILL.md
```

### 6. Restart Claude Code

```bash
# Quit and relaunch
claude
```

The agents are now available via `@` mentions in any Claude Code conversation.

---

## Placeholders

Files in this repo are sanitized — anything specific to one machine or one Notion
workspace is written as a `[YOUR_*]` token rather than a real value. Nothing here
is a secret, but none of it is portable either, so substitute before use.

| Placeholder | What to substitute |
|---|---|
| `[YOUR_AGY_PATH]` | Absolute path to your `agy` binary — `which agy` |
| `[YOUR_PROJECTS_PATH]` | Your projects root, e.g. `~/Projects` |
| `[YOUR_INVESTING_TOOL_PATH]` | Where the investing tool lives, if you use those archived skills |
| `[YOUR_DRIVE_FOLDER_ID]` | Google Drive folder ID the skill should read |
| `[YOUR_SHEET_ID]` | Google Sheet ID |
| `[YOUR_TODOIST_UID]` | Your Todoist user ID |
| `[YOUR_APP_URL]` | Base URL of your own deployed app |
| `[YOUR_*_DB_ID]` / `[YOUR_*_DS_ID]` | Notion database and data-source IDs, per skill |
| `[PARTNER]`, `[Company]` | Names the skill would otherwise hardcode |

Grep for what's left before you run anything:

```bash
grep -rn "\[YOUR_" ~/.claude/skills ~/.claude/agents
```

---

## How to Use the Agents

### Calling an agent

Type `@` followed by the agent name in any Claude Code conversation:

```
@gemini-researcher analyze how the authentication system works in this codebase
@gemini-builder add a CSV export feature to the reports page
@gemini-security-reviewer audit this before I deploy
```

Claude Haiku orchestrates: it decides whether to handle something inline or route it to a specialist. You can also invoke agents directly for specific tasks.

### The mandatory two-stage workflow (gemini-builder)

`@gemini-builder` always uses a two-stage workflow. This is non-negotiable — it prevents accidental file overwrites.

**Stage 1 — Plan (no file writes):**
```bash
agy --add-dir /path/to/project --print "PLAN ONLY — do not write any files. Task: [your task]. List every file you would create or modify and what changes each requires."
```

The agent presents the plan. You review it and approve or reject.

**Stage 2 — Execute (only after you approve):**
```bash
# First, create a safety branch
git checkout -b agy/$(date +%Y%m%d-%H%M%S)

# Then execute
agy --dangerously-skip-permissions --add-dir /path/to/project --print "Execute this approved plan: [plan text]"
```

After execution:
```bash
git diff          # review what changed
git add -p        # stage selectively
git commit -m "feat: [description]"
```

---

## Create a GEMINI.md in Every Project

Every project that uses these agents needs a `GEMINI.md` in the root. The agents check for it and will stop if it's missing. It gives `agy` the context it needs to produce accurate output.

**Minimum viable `GEMINI.md`:**

```markdown
# Project: [Name]

## What this is
[One paragraph describing what the project does and why it exists]

## Architecture
[Bullet list: what the main components are, how they connect]

## Tech stack
[Language, framework, database, hosting, key libraries]

## Key files
- `app/main.py` — entry point, route definitions
- `app/models.py` — data models
- `app/config.py` — configuration and secrets

## Conventions
[Anything non-obvious: naming patterns, SQL style, API response shapes, etc.]

## Known gotchas
[Things that have bitten you or that would surprise a new contributor]
```

The more specific, the better. `agy` uses this to avoid generic answers and hallucinated patterns.

---

## Agent Reference

| Agent | When to use | Safety tier |
|-------|------------|-------------|
| `@gemini-researcher` | Understand a codebase, validate an implementation, run QA, trace data flows, audit for patterns | Read-only. Never writes files. |
| `@gemini-builder` | Write code, build features, make changes to 2+ files | Two-stage: plan → approve → execute. Always uses a git branch. |
| `@gemini-analyst` | Research, competitive analysis, evaluate documents, financial modeling | Read-only (produces reports only). |
| `@gemini-security-reviewer` | Security audit before any deployment, auth flows, user data handling | Read-only. Never auto-fixes. All findings escalate to you. |
| `@outcome-architect` | Starting a new project, defining "done", auditing a plan before gemini-builder starts | Read-only. Blocks gemini-builder until it issues a `/goal` authorization. |

### Typical workflow for a new feature

```
1. @outcome-architect — define what success looks like
2. @gemini-researcher — understand the codebase, identify what to change
3. @gemini-builder   — plan → approve → execute
4. @gemini-researcher — validate the build against the spec
5. @gemini-security-reviewer — audit before deploying
```

---

## Key `agy` Flags

| Flag | Purpose | When to use |
|------|---------|------------|
| `--add-dir /path` | Adds a directory to agy's context | Always — gives it the project to work with |
| `--print "prompt"` | Non-interactive, outputs to stdout | All agent workflows (safe by default) |
| `--dangerously-skip-permissions` | Auto-approves all file writes | Stage 2 only, after plan approval |
| `--sandbox` | Restricts agy to the workspace directory | Optional — for exploratory tasks on unfamiliar codebases |

**Never use `--dangerously-skip-permissions` without an approved plan and a git safety branch.**

---

## Quota Failover

`agy` and `gemini` (if you have both installed) run on separate Google accounts / quotas. When one is exhausted (you'll see a 429 / `RESOURCE_EXHAUSTED` error), switch to the other for the rest of the session.

```bash
# Default: use agy
agy --add-dir /path --print "..."

# Fallback when agy quota is exhausted: use gemini
gemini --yolo -p "PLAN ONLY — do not write any files. Task: [...]. $(cat app/relevant_file.py)"
```

`gemini` lacks `--add-dir`; deliver context via inline `$(cat file)` — cap at ~30k chars.

---

## Safety Rules (Non-Negotiable)

1. **Always create a git branch before Stage 2 execute.** `git checkout -b agy/$(date +%Y%m%d-%H%M%S)`
2. **Always review `git diff` before committing.** `agy` writes what it's told — confirm what landed.
3. **Never use `--dangerously-skip-permissions` for read-only tasks** (research, analysis, review).
4. **gemini-security-reviewer never auto-fixes.** All findings are presented to you; you decide.
5. **outcome-architect blocks gemini-builder.** No implementation starts until a `/goal` authorization is issued.

---

## Adapting This for Your Setup

These agent files are tuned for one specific environment (Python/Modal/Cloudflare stack, macOS). To adapt them:

1. **Update the binary path** in each agent `.md` — replace `[YOUR_AGY_PATH]` with your actual `agy` path (see §Installation step 5 above).
2. **Update stack references** in `gemini-security-reviewer.md` — it defaults to `Python · Modal · FastAPI · HTMX · Cloudflare · SQLite`. Change `Stack defaults:` to match your stack.
3. **Update GEMINI.md** in each project — no stack assumptions, just describe your project.

Everything else is stack-agnostic.

---

## Troubleshooting

**`agy: command not found`**
Run `which agy` or `pip show antigravity-cli`. If installed but not found, add `~/.local/bin` to your `PATH`:
```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

**Agent says "No GEMINI.md found"**
Create one in your project root (see the GEMINI.md section above).

**`RESOURCE_EXHAUSTED` / 429 from agy**
You've hit your quota. Switch to `gemini` fallback for the session, or wait for the quota window to reset (typically hourly or daily depending on your tier).

**Claude Haiku keeps handling tasks inline instead of delegating**
Be explicit: `@gemini-builder [task]`. Or tell Claude: "Delegate this to @gemini-builder using the two-stage workflow."

**Plan looks wrong after Stage 1**
Don't approve it. Tell the agent what's wrong and ask for a revised plan. Stage 2 only runs after you explicitly approve.

---

## File Structure of This Repo

```
productivity/
├── SETUP.md          ← you are here
├── README.md         ← overview and install summary
├── agents/           ← agent definition files (.md) — copy to ~/.claude/agents/
│   ├── gemini-researcher.md
│   ├── gemini-builder.md
│   ├── gemini-analyst.md
│   ├── gemini-security-reviewer.md
│   └── outcome-architect.md
├── skills/           ← slash-command skills — copy to ~/.claude/skills/
└── plugins/          ← Claude Cowork plugin definitions
```

---

*These agents were developed for a personal AI engineering workflow. Use, fork, and adapt freely.*
