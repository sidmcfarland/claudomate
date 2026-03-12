# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.2.0] - 2026-03-12

### Added
- `SessionStart` hook: the plugin now automatically injects a scan summary at
  the start of every session — no manual CLAUDE.md setup required
- `config.json` (`~/.claude/claudomate/config.json`): new settings file that
  controls plugin state. Currently supports `enabled` (boolean). Checked by
  the session-start hook on every session.

### Changed
- `/claudomate:start` now enables the session-start hook by writing
  `enabled: true` to `config.json` instead of modifying CLAUDE.md
- `/claudomate:stop` now disables the session-start hook by writing
  `enabled: false` to `config.json` instead of modifying CLAUDE.md
- `/claudomate:remove` no longer touches CLAUDE.md; `config.json` is cleaned
  up automatically as part of the working directory deletion

### Removed
- Manual CLAUDE.md hook requirement: the session-start scan previously
  required users to add a line to their CLAUDE.md. This is no longer needed.

## [1.1.0] - 2026-03-12

### Added
- `/claudomate:start` skill: adds session-start hook to CLAUDE.md (project or global) and restores any suspended agent cron schedules
- `/claudomate:stop` skill: removes session-start hook and suspends all claudomate-managed cron schedules; preserves all files and data for resumption
- `/claudomate:kill` skill: removes a single deployed agent (cron entry, project directory, registry entry)
- `/claudomate:remove` skill: full uninstall — removes hook, kills all agents, deletes working files, instructs plugin removal

### Changed
- Installation step 2 is now handled by `/claudomate:start` instead of manual CLAUDE.md editing
- Uninstallation is now handled by `/claudomate:remove` instead of manual steps

## [1.0.0] - 2026-03-12

### Added
- Claudomate subagent with four operating modes: observation, interrogation, deployment, and monitoring
- `/claudomate:scan` skill for session-start proposal and monitoring checks
- `/claudomate:build` skill for interactive workflow modeling
- Plugin manifest and structure for Claude Code marketplace distribution
