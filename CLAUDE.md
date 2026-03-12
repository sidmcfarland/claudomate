# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Claudomate is a Claude Code plugin (not a standalone app — no package.json, no build system, no tests). It discovers repetitive user workflows, models them through interrogation, and deploys self-contained Claude Code agents to execute them on schedule via system cron.

## Repository Structure

- **`agents/claudomate.md`** — The core subagent definition. Contains all four operating modes (observation, interrogation, deployment, monitoring) and is the primary file you'll edit for behavior changes. Uses `model: opus` and has access to `Read, Write, Edit, Bash, Glob, Grep, AskUserQuestion`.
- **`hooks/hooks.json`** — Defines the `SessionStart` hook that injects scan results into context at the start of every session.
- **`skills/scan/SKILL.md`** — Reads proposals and monitoring logs and surfaces findings to the user. Invoked as `/claudomate:scan`.
- **`skills/build/SKILL.md`** — Invokes the claudomate subagent for interactive workflow modeling. Invoked as `/claudomate:build`.
- **`skills/start/SKILL.md`** — Enables session scanning and restores suspended cron schedules. Invoked as `/claudomate:start`.
- **`skills/stop/SKILL.md`** — Disables session scanning and suspends all agent cron schedules. Invoked as `/claudomate:stop`.
- **`skills/kill/SKILL.md`** — Removes a single deployed agent. Invoked as `/claudomate:kill`.
- **`skills/remove/SKILL.md`** — Full uninstall. Invoked as `/claudomate:remove`.
- **`.claude-plugin/plugin.json`** — Plugin manifest (name, version, metadata, hooks reference).

## Key Architecture Decisions

- **Deployed agents are standalone Claude Code projects**, not subagents. Each gets its own directory (`~/agents/{name}/`), CLAUDE.md, skills, settings.json, and cron entry. Zero runtime dependency on the main agent.
- **All working state lives in `~/.claude/claudomate/`** — proposals.json, models/, monitoring.json, monitoring-report.json, and logs/. This is the subagent's working directory, not part of this repo.
- **Session-start scanning is handled by a `SessionStart` hook** defined in `hooks/hooks.json`. The hook checks `~/.claude/claudomate/config.json` for `enabled: false` before running, and exits silently when there is nothing to report. No manual CLAUDE.md setup is required.

## Development

**Local testing:**
```bash
claude --plugin-dir ./claudomate
```

**Renaming the plugin** requires updating four locations: plugin.json `name`, agent frontmatter `name`, working directory path in the agent prompt, and skill file references to `claudomate`.
