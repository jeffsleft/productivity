---
name: agent-delegation
description: Fire when delegating work to Antigravity CLI (agy), running gemini-builder/gemini-researcher, managing agy quota failover, or using two-stage plan/execute workflow
version: 1.0.0
---

# Antigravity CLI — Agent Delegation Standard

Google deprecated Gemini CLI on June 18, 2026, replacing it with **Antigravity CLI** (`agy` at `/Users/jeffbeaumont/.local/bin/agy`). All subagents that previously called `gemini` now call `agy`.

## Quota failover — `agy` ↔ `gemini`

`agy` and `gemini` run on **separate quotas** (separate Google accounts). When one is exhausted, switch to the other for the remainder of the session. Default to `agy`; fall back to `gemini` on quota exhaustion.

**Detecting exhaustion:** A quota error (429 / `RESOURCE_EXHAUSTED`) from `agy` is the signal to switch. Do not retry `agy` — switch immediately.

**`gemini` fallback syntax** (mirrors the two-stage workflow):
- Stage 1 (plan): `gemini --yolo -p "PLAN ONLY — do not write any files. Task: [task]. $(cat /path/to/file.py)"`
- Stage 2 (execute): `gemini --yolo -p "Execute this approved plan: [plan]"`
- Read-only: `gemini --yolo -p "Research/analyze: [task]. Do not write any files."`

`gemini` lacks `--add-dir`; deliver context via inline `$(cat file)` — cap at ~30k chars. All other safety rules (two-stage gate, `git diff` review, no execute without approval) apply identically regardless of which binary is in use.

## Binary location
```
/Users/jeffbeaumont/.local/bin/agy
```

## Key flags
| Flag | Purpose |
|------|---------|
| `--print "prompt"` | Non-interactive, outputs to stdout. Safe by default — Antigravity will prompt for permission before file writes. |
| `--dangerously-skip-permissions` | Auto-approves all file writes and terminal commands. Only use in Stage 2 (execute) after explicit user approval. |
| `--add-dir /path` | Adds a directory to Antigravity's workspace context. Repeatable. Prefer over piping file contents. |
| `--sandbox` | Restricts Antigravity to the workspace directory — blocks access to system paths. Use for untrusted or exploratory tasks. |

## Mandatory two-stage workflow for any implementation task

**Stage 1 — Plan (no file writes, no `--dangerously-skip-permissions`):**
```bash
/Users/jeffbeaumont/.local/bin/agy --add-dir /path/to/project --print "PLAN ONLY — do not write any files. Task: [task]. List every file you would create or modify and what changes each requires."
```
Present plan to user or Claude orchestrator. Wait for explicit approval.

**Stage 2 — Execute (only after approval):**
```bash
/Users/jeffbeaumont/.local/bin/agy --dangerously-skip-permissions --add-dir /path/to/project --print "Execute this approved plan: [approved plan text]"
```
Review `git diff` after execution before committing.

## Read-only tasks (research, analysis, security review)
```bash
/Users/jeffbeaumont/.local/bin/agy --add-dir /path/to/project --print "Research/analyze: [task]. Do not write any files."
```
Never use `--dangerously-skip-permissions` for read-only tasks.

## Safety tiers
1. **Default** (`--print` only): Antigravity prompts for permission before any file write. Used for all read-only agents.
2. **Sandbox** (`--sandbox --print`): Restricts to workspace directory. Optional override for exploratory tasks on unfamiliar codebases — not the default because it blocks `/tmp/` and system paths needed by some workflows.
3. **Approved execute** (`--dangerously-skip-permissions --print`): Only after Stage 1 plan was reviewed and approved. Always preceded by `git checkout -b agy/$(date +%Y%m%d-%H%M%S)` and followed by `git diff` review before committing. Rollback: `git checkout main && git branch -D agy/<branch>`.

## Context delivery
- Use `--add-dir` to give Antigravity project context — do not pipe raw file contents via stdin.
- For inline single-file content: embed in the prompt string via `$(cat file.py)` — cap at ~30k chars.

## Subagent role mapping
| Role | agy flags | Notes |
|------|-----------|-------|
| gemini-researcher | `--add-dir --print` | Read-only always |
| gemini-analyst | `--add-dir --print` or inline | Read-only always |
| gemini-security-reviewer | `--add-dir --print` | Read-only always; never auto-fix |
| gemini-builder | Stage 1: `--add-dir --print` → Stage 2: `--dangerously-skip-permissions --add-dir --print` | Requires approval gate |
| outcome-architect | `--add-dir --print` | Read-only always |
