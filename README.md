# productivity

A collection of Claude Code skills, plugins, and agent definitions for personal productivity, software engineering, and AI-augmented workflows.

## Structure

```
productivity/
├── skills/     Claude Code slash-command skills (/skill-name)
├── plugins/    Claude Cowork plugin definitions
└── agents/     Subagent role definitions (gemini-*, outcome-architect, etc.)
```

## Skills

Each skill is a directory containing a `SKILL.md` with YAML frontmatter (`name`, `description`, `version`). Install by placing the directory under `~/.claude/skills/`.

| Skill | Description |
|-------|-------------|
| [ship-check](skills/ship-check/) | Pre-merge quality gate — static code review + Puppeteer UI tests |
| [skill-creator](skills/skill-creator/) | Scaffold a new Claude Code skill |
| [define-done](skills/define-done/) | Outcome Definition Process for new projects (ODF) |
| [lessons-learned](skills/lessons-learned/) | Capture technical failures and fixes into project memory |
| [lessons-learned-review](skills/lessons-learned-review/) | Promote lessons to permanent AI_RULES |
| [consolidate-memory](skills/consolidate-memory/) | Merge duplicate memory files, fix stale facts |
| [pre-ship-security-checklist](skills/pre-ship-security-checklist/) | Security review before any deploy |
| [doc-coauthoring](skills/doc-coauthoring/) | Structured co-authoring workflow for docs and specs |
| [execution-shadow](skills/execution-shadow/) | Monitor and unblock long-running tasks |
| [seo-aeo-audit](skills/seo-aeo-audit/) | SEO + Answer Engine Optimization audit |
| [setup-cowork](skills/setup-cowork/) | Configure Claude Cowork with role-matched plugins |
| [grill-me](skills/grill-me/) | Interview practice — asks you tough questions |
| [grill-with-docs](skills/grill-with-docs/) | Interview practice grounded in your own documents |
| [walk-and-talk-interviewer](skills/walk-and-talk-interviewer/) | Voice-mode interview preparation |
| [story-brand](skills/story-brand/) | StoryBrand framework for messaging and positioning |
| [article-rewrite](skills/article-rewrite/) | Rewrite a draft to sound human and specific |
| [blog-graphic-design](skills/blog-graphic-design/) | Generate graphic design briefs for blog posts |
| [caveman](skills/caveman/) | Simplify complex writing to plain language |
| [voice-engine](skills/voice-engine/) | Extract and codify a personal writing voice |
| [todoist-cli](skills/todoist-cli/) | Todoist task management from Claude Code |
| [todoist-organizer](skills/todoist-organizer/) | Audit and reorganize a Todoist project |
| [weekly-ai-chat-review](skills/weekly-ai-chat-review/) | Review open AI chats for Todoist follow-ups |
| [schedule](skills/schedule/) | Create or manage scheduled Claude Code routines |
| [docx](skills/docx/) | Generate .docx documents |
| [pdf](skills/pdf/) | Generate PDFs |
| [pptx](skills/pptx/) | Generate PowerPoint presentations |
| [xlsx](skills/xlsx/) | Generate Excel spreadsheets |
| [annotatable-pdf-creation](skills/annotatable-pdf-creation/) | PDFs formatted for iPad annotation |

## Agents

Subagent role definitions for use with the Claude Code Agent tool.

| Agent | Role |
|-------|------|
| [gemini-researcher](agents/gemini-researcher.md) | Deep codebase analysis and research |
| [gemini-builder](agents/gemini-builder.md) | Implementation specialist |
| [gemini-analyst](agents/gemini-analyst.md) | Investment research and competitive analysis |
| [gemini-security-reviewer](agents/gemini-security-reviewer.md) | Security audit specialist |
| [outcome-architect](agents/outcome-architect.md) | Strategic quality gate and outcome definition |

## Plugins

Coming soon.
