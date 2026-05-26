# productivity

Skills, agents, and plugins for Claude Code. Personal productivity, software engineering, and AI-augmented workflows.

## Structure

```
productivity/
├── skills/     Claude Code slash-command skills (/skill-name)
├── agents/     Subagent role definitions (gemini-*, outcome-architect, etc.)
└── plugins/    Claude Cowork plugin definitions
```

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — required for skills and agents
- [Antigravity CLI](https://antigravity.dev) (`agy` at `/Users/jeffbeaumont/.local/bin/agy`) — required for Gemini agents (`gemini-builder`, `gemini-researcher`, `gemini-analyst`, `gemini-security-reviewer`)
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
| [story-brand](skills/story-brand/) | StoryBrand framework for messaging and positioning |
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
| Skill | Description |
|-------|-------------|
| [cos-sync](skills/cos-sync/) | Full 3-phase Chief of Staff session orchestrator |
| [cos-sync-friday](skills/cos-sync-friday/) | Friday CoS review |
| [friday-cos-review](skills/friday-cos-review/) | Weekly Friday output: CoS review + Happy Friday email |
| [execution-shadow](skills/execution-shadow/) | Shadow execution of a task or session plan |
| [weekly-review](skills/weekly-review/) | Full weekly review across Todoist, Notes, and projects |
| [wins-rollup](skills/wins-rollup/) | Monthly wins synthesis across all active projects |
| [todoist-cli](skills/todoist-cli/) | Todoist task management from Claude Code |
| [todoist-organizer](skills/todoist-organizer/) | Audit and reorganize a Todoist project |
| [weekly-ai-chat-review](skills/weekly-ai-chat-review/) | Review open AI chats for Todoist follow-ups |
| [glm-wednesday-email-draft](skills/glm-wednesday-email-draft/) | Draft the weekly GLM Finance email |
| [ca-move-pulse](skills/ca-move-pulse/) | California move logistics tracker |

### Project & Memory Management
| Skill | Description |
|-------|-------------|
| [define-done](skills/define-done/) | Outcome Definition Process (ODF) for new projects |
| [schedule](skills/schedule/) | Create and manage scheduled remote agents |
| [doc-coauthoring](skills/doc-coauthoring/) | Structured co-authoring workflow |
| [setup-cowork](skills/setup-cowork/) | Configure Claude Cowork with role-matched plugins |
| [consolidate-memory](skills/consolidate-memory/) | Merge duplicate memory files, fix stale facts |

### Investing Tool (flat `.md` files — Cowork/context injection)
| File | Description |
|------|-------------|
| [analyze-portfolio.md](skills/analyze-portfolio.md) | Full portfolio analysis |
| [analyze-stock.md](skills/analyze-stock.md) | Single ticker deep dive |
| [ceo-score.md](skills/ceo-score.md) | CEO/leadership quality scorecard |
| [add-source.md](skills/add-source.md) | Add research to Source Library |
| [smart-money.md](skills/smart-money.md) | Institutional ownership lookup |
| [goal-status.md](skills/goal-status.md) | Financial goal CAGR tracker |
| [patterns.md](skills/patterns.md) | Calibration and pattern review |
| [retrospective.md](skills/retrospective.md) | Quarterly portfolio retrospective |
| [stress.md](skills/stress.md) | Portfolio stress testing |
| [tax-advisor.md](skills/tax-advisor.md) | Tax-loss harvesting and tax event analysis |
| [journal.md](skills/journal.md) | Investment decision journaling |
| [deploy-test.md](skills/deploy-test.md) | Post-deploy verification for investing app |

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

All Gemini agents use the Antigravity CLI (`agy`) at `/Users/jeffbeaumont/.local/bin/agy`. `agy` and the deprecated `gemini` CLI run on separate quotas — if one is exhausted, switch to the other for the session.

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
