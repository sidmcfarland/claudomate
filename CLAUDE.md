# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Claudomate is a Claude Code plugin (not a standalone app — no package.json, no build system, no tests). It discovers repetitive user workflows, models them through interrogation, and deploys self-contained Claude Code agents to execute them on schedule via system cron.

## Repository Structure

This is a Claude Code plugin with three components:

- **`agents/claudomate.md`** — The core subagent definition. Contains all four operating modes (observation, interrogation, deployment, monitoring) and is the primary file you'll edit for behavior changes. Uses `model: opus` and has access to `Read, Write, Edit, Bash, Glob, Grep, AskUserQuestion`.
- **`skills/check-claudomate/SKILL.md`** — Session-start skill that reads proposals and monitoring logs. Invoked as `/claudomate:check-claudomate`.
- **`skills/interrogate/SKILL.md`** — Skill that invokes the claudomate subagent for interactive workflow modeling. Invoked as `/claudomate:interrogate`.
- **`.claude-plugin/plugin.json`** — Plugin manifest (name, version, metadata).

## Key Architecture Decisions

- **Deployed agents are standalone Claude Code projects**, not subagents. Each gets its own directory (`~/agents/{name}/`), CLAUDE.md, skills, settings.json, and cron entry. Zero runtime dependency on the main agent.
- **All working state lives in `~/.claude/claudomate/`** — proposals.json, models/, monitoring.json, monitoring-report.json, and logs/. This is the subagent's working directory, not part of this repo.
- **The plugin requires a manual CLAUDE.md hook** — users must add a line to their CLAUDE.md to run `/claudomate:check-claudomate` at session start. Without this, proposals and alerts are never surfaced.

## Development

**Local testing:**
```bash
claude --plugin-dir ./claudomate
```

**Renaming the plugin** requires updating four locations: plugin.json `name`, agent frontmatter `name`, working directory path in the agent prompt, and skill file references to `claudomate`.
