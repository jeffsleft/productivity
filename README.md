# productivity

Skills, agents, and plugins for Claude Code. Personal productivity, software engineering, and AI-augmented workflows.

## Structure

```
productivity/
├── skills/     Claude Code slash-command skills (/skill-name)
├── agents/     Subagent role definitions (gemini-*, outcome-architect, etc.)
└── plugins/    Claude Cowork plugin definitions
```

## Getting Started

**New here?** See [SETUP.md](SETUP.md) for a full walkthrough: prerequisites, installation, the two-stage build workflow, agent roles, GEMINI.md setup, safety rules, and quota failover. Designed to be readable by both humans and AI agents — point your AI at this repo and it can follow along.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — required for skills and agents
- [Antigravity CLI](https://antigravity.dev) (`agy`) — required for Gemini agents (`gemini-builder`, `gemini-researcher`, `gemini-analyst`, `gemini-security-reviewer`)
- Todoist MCP + Notion MCP — required for CoS and productivity skills

## Installation

**Skills:** Copy the directory to `~/.claude/skills/` and restart Claude Code.
```bash
cp -r skills/ship-check ~/.claude/skills/
```

**Agents:** Copy `.md` files to `~/.claude/agents/`.
```bash
cp agents/*.md ~/.claude/agents/
```

Update the `agy` binary path in each agent file to match your system (`which agy`). See [SETUP.md](SETUP.md) for the one-liner.

Each project that uses the Gemini agents needs a `GEMINI.md` in the project root with project context (architecture, stack, conventions). This significantly improves output quality.

---

## Skills

Skills are slash commands (`/skill-name`). Two formats:
- **Directory skills** (`<name>/SKILL.md`) — proper Claude Code format, registerable to a Claude account
- **Flat `.md` files** — workflow definitions for Cowork and Code context injection; not registerable as slash commands

### Writing & Content
| Skill | Description |
|-------|-------------|
| [draft-blog-post](skills/draft-blog-post/) | Draft posts for jeffreybeaumont.com from voice memos, notes, or briefs |
| [public-writing](skills/public-writing/) | Voice and style review for public-facing content |
| [internal-writing](skills/internal-writing/) | Mercy Ships / leadership internal communication standards |
| [article-rewrite](skills/article-rewrite/) | Rewrite generic drafts with specific voice |
| [seo-aeo-audit](skills/seo-aeo-audit/) | SEO and AEO audit for blog content |
| [storybrand](skills/storybrand/) | StoryBrand framework for messaging and positioning |
| [voice-engine](skills/voice-engine/) | Extract and codify a personal writing voice |

### Document Production
| Skill | Description |
|-------|-------------|
| [docx](skills/docx/) | Generate .docx documents |
| [pdf](skills/pdf/) | Generate PDFs |
| [pptx](skills/pptx/) | Generate PowerPoint presentations |
| [xlsx](skills/xlsx/) | Generate Excel spreadsheets |
| [annotatable-pdf-creation](skills/annotatable-pdf-creation/) | iPad-ready annotatable PDF format |
| [publish-to-wordpress](skills/publish-to-wordpress/) | Publish posts to jeffreybeaumont.com via WordPress MCP |
| [blog-graphic-design](skills/blog-graphic-design/) | Generate graphic design briefs for blog posts |

### Engineering Conventions (on-demand, load when in the domain)
| Skill | Trigger |
|-------|---------|
| [gemini-api-conventions](skills/gemini-api-conventions/) | Gemini API calls, model strategy, rate limits |
| [scraping-conventions](skills/scraping-conventions/) | Web scraping, ATS fetching, Jina/Firecrawl/curl_cffi |
| [modal-conventions](skills/modal-conventions/) | Modal functions, deploy safety, secrets, route collisions |
| [cloudflare-stack-rules](skills/cloudflare-stack-rules/) | Cloudflare Workers/Pages/KV, wrangler.toml |
| [database-conventions](skills/database-conventions/) | SQLite, Supabase, migrations, schema design |
| [notion-conventions](skills/notion-conventions/) | Notion REST API, property types, idempotent writes |
| [python-style](skills/python-style/) | Python code, f-strings, Jinja2, YAML config |
| [frontend-htmx-conventions](skills/frontend-htmx-conventions/) | HTMX + Jinja2, redirects, partial-href traps |
| [agent-delegation](skills/agent-delegation/) | Antigravity CLI, two-stage plan/execute, quota failover |

### Coding & Engineering
| Skill | Description |
|-------|-------------|
| [ship-check](skills/ship-check/) | Pre-merge quality gate — static code review + Puppeteer UI tests |
| [lessons-learned](skills/lessons-learned/) | Log technical failures and fixes at session end |
| [lessons-learned-review](skills/lessons-learned-review/) | Promote lessons to permanent AI_RULES |
| [pre-ship-security-checklist](skills/pre-ship-security-checklist/) | Security checklist before deploying any app |
| [skill-creator](skills/skill-creator/) | Scaffold a new Claude Code skill |

### Research & Analysis
| Skill | Description |
|-------|-------------|
| [grill-me](skills/grill-me/) | Socratic interview against a document or argument |
| [grill-with-docs](skills/grill-with-docs/) | Deep Q&A against a document set |
| [walk-and-talk-interviewer](skills/walk-and-talk-interviewer/) | Voice-first interview workflow |
| [caveman](skills/caveman/) | Simplify complex concepts to plain language |

### Productivity & Chief of Staff

> **The six CoS skills work as a set.** Start with the
> **[Chief of Staff plugin README](plugins/chief-of-staff/)** — it explains the sequence,
> what it requires, and whether it's a fit before you install anything.

| Skill | Description |
|-------|-------------|
| [cos-sync](skills/cos-sync/) | Orchestrator — runs the full CoS session in sequence |
| [walk-and-talk-interviewer](skills/walk-and-talk-interviewer/) | Phase 1 — voice interview that turns vague tasks into clear ones |
| [todoist-organizer](skills/todoist-organizer/) | Phase 2 — audit, prioritize, and tag tasks by AI leverage |
| [execution-shadow](skills/execution-shadow/) | Phase 3 — work the 🤖 Claude-Ready queue |
| [decision-accountability](skills/decision-accountability/) | Standing — surface decisions that were made and then abandoned |
| [weekly-review](skills/weekly-review/) | Weekly strategic pass across Todoist, Notes, and the project sheet |
| [wins-rollup](skills/wins-rollup/) | Monthly wins synthesis across all active projects |

### Project & Memory Management
| Skill | Description |
|-------|-------------|
| [define-done](skills/define-done/) | Outcome Definition Process (ODF) for new projects |
| [schedule](skills/schedule/) | Create and manage scheduled remote agents |
| [doc-coauthoring](skills/doc-coauthoring/) | Structured co-authoring workflow |
| [setup-cowork](skills/setup-cowork/) | Configure Claude Cowork with role-matched plugins |
| [consolidate-memory](skills/consolidate-memory/) | Merge duplicate memory files, fix stale facts |

### Investing Tool — archived

The eleven investing skills (`analyze-portfolio`, `analyze-stock`, `ceo-score`,
`add-source`, `smart-money`, `goal-status`, `journal`, `patterns`, `retrospective`,
`stress`, `tax-advisor`) moved to [`skills/_archive/`](skills/_archive/) on 2026-08-20.
They are kept for reference and are not maintained.

| Skill | Description |
|-------|-------------|
| [deploy-test](skills/deploy-test/) | Post-deploy test for the investing-insights app (still active) |

---

## Agents

Subagent role definitions. Install by copying `.md` files to `~/.claude/agents/`.

| Agent | Role |
|-------|------|
| [gemini-researcher](agents/gemini-researcher.md) | Large-codebase analysis, QA, and validation |
| [gemini-builder](agents/gemini-builder.md) | Implementation specialist — two-stage plan/execute |
| [gemini-analyst](agents/gemini-analyst.md) | Investment research and competitive analysis |
| [gemini-security-reviewer](agents/gemini-security-reviewer.md) | Adversarial security audit — never auto-fixes |
| [outcome-architect](agents/outcome-architect.md) | ODF quality gate — blocks builder until charter approved |

All Gemini agents use the Antigravity CLI (`agy`) at `[YOUR_AGY_PATH]`. `agy` and the deprecated `gemini` CLI run on separate quotas — if one is exhausted, switch to the other for the session.

---

## Philosophy

Two-model architecture: Claude Code handles orchestration, file I/O, and decisions; `agy` handles large-context work (codebases, document sets, security sweeps). Neither model does everything — they work as a team.

The `outcome-architect` + `gemini-researcher` + `gemini-builder` + `gemini-security-reviewer` pipeline enforces a discipline:

1. Define done before writing code (`outcome-architect`)
2. Research and plan before building (`gemini-researcher`)
3. Build with full codebase context (`gemini-builder`)
4. Validate before shipping (`gemini-researcher` + `gemini-security-reviewer`)

## Platform Notes

Skills are NOT universal across platforms by default.

| Platform | Skill source | How to add |
|----------|-------------|-----------|
| **Claude Code** | `~/.claude/skills/<name>/SKILL.md` | Copy the directory |
| **Claude Cowork** | Cloud account, synced locally | Add via claude.ai UI → Settings → Skills |
| **Claude Chat** | Cloud account only | Add via claude.ai UI → Settings → Skills |

Flat `.md` skills are for context injection in Cowork or Code sessions — not registerable as slash commands.
