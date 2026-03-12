# Claudomate

A Claude Code plugin that discovers your repetitive workflows and deploys autonomous agents to handle them for you.

## What it does

Claudomate is a subagent that progressively automates your routine tasks. It works in four phases:

1. **Observe** — Scans your agent's memory and session history for repetitive patterns
2. **Propose** — Surfaces candidate workflows and asks if you'd like them automated
3. **Interrogate** — Asks targeted questions to fully model a workflow's steps, tools, edge cases, and schedule
4. **Deploy** — Creates a self-contained Claude Code project with its own CLAUDE.md, skills, and cron schedule to execute the workflow autonomously

Over time, claudomate builds a growing ecosystem of purpose-built agents that handle your routine work so you can focus on tasks that genuinely require your judgment.

## Skills

| Skill | Purpose |
|---|---|
| `/claudomate:start` | Add the session-start hook to CLAUDE.md (creates the file if needed) |
| `/claudomate:scan` | Check for pending proposals and agent health alerts |
| `/claudomate:build` | Interactively model a workflow for automation |
| `/claudomate:stop` | Remove the session-start hook from CLAUDE.md (pauses claudomate, leaves agents intact) |
| `/claudomate:kill` | Remove a specific deployed agent (cron entry + directory + registry) |
| `/claudomate:remove` | Full uninstall: stop + kill all agents + delete working files |

> **Note:** Plugin skills don't appear in slash command autocomplete. Type the full command — it will work even though nothing shows up as you type.

## Installation

### Step 1: Install the plugin

**From the official Claude Code marketplace (coming soon):**

Claudomate is still under development and will be submitted to the official marketplace once stable.

**From GitHub:**

```
/plugin marketplace add sidmcfarland/claudomate
/plugin install claudomate@sidmcfarland
```

**For local development:**

```bash
git clone https://github.com/sidmcfarland/claudomate.git
claude --plugin-dir ./claudomate
```

### Step 2: Run `/claudomate:start`

This activates claudomate by adding a session-start hook to your CLAUDE.md. It
will ask whether to activate for this project only or globally across all projects,
then create or update the file automatically.

## Uninstallation

### To fully uninstall

Run `/claudomate:remove`. This will:
- Remove the session-start hook from CLAUDE.md
- Kill all deployed agents (cron entries + directories)
- Delete all claudomate working files

Then uninstall the plugin itself:

```
/plugin uninstall claudomate@sidmcfarland
```

### To retire a single agent

Run `/claudomate:kill` to remove one deployed agent's cron entry, directory,
and registry entry without affecting anything else.

### To pause without uninstalling

Run `/claudomate:stop` to remove the session-start hook while leaving all
deployed agents and working files intact. Run `/claudomate:start` to reactivate.

## Usage

### Automatic discovery

Once installed, claudomate can be invoked to scan for automatable workflows:

```
Scan my workflows for anything that could be automated.
```

It reads your agent's memory for patterns — things you do repeatedly, on a schedule, or that you've mentioned as routine tasks. Candidates are written to a proposals log.

### Reviewing proposals

At the start of each session (if you ran `/claudomate:start`), your agent checks for pending proposals and summarizes them. You can also check manually:

```
/claudomate:scan
```

### Modeling a workflow

When you approve a proposal, start the interactive modeling process:

```
/claudomate:build
```

Claudomate asks you targeted questions to fully specify the workflow — triggers, steps, tools, success criteria, error handling, and schedule. This may span multiple sessions; progress is saved automatically.

### Deployed agents

When a workflow is fully modeled and you approve it, claudomate creates:

- A new project directory (default: `~/agents/{agent-name}/`)
- A complete `CLAUDE.md` with all workflow instructions
- Skills for discrete capabilities (e.g., reading emails, categorizing transactions)
- MCP server configuration for external service access
- A system cron entry to run the agent on your existing schedule
- Structured logging for monitoring and debugging

Deployed agents run headlessly via `claude -p` and are fully self-contained. They never depend on your main agent's context at runtime.

### Monitoring

Claudomate periodically reviews deployed agent logs for failures, information gaps, and anomalies. Issues are surfaced through the session-start check.

## How it works

### Plugin components

| Component | Purpose |
|---|---|
| `agents/claudomate.md` | The core subagent definition with all four operating modes |
| `skills/scan/` | Session-start skill that reads proposals and monitoring logs |
| `skills/build/` | Skill that invokes claudomate for interactive workflow modeling |

### File locations

Claudomate stores its working files at `~/.claude/claudomate/`:

```
~/.claude/claudomate/
├── proposals.json            # Candidate workflows
├── models/                   # In-progress workflow models
├── monitoring.json           # Deployed agent registry
├── monitoring-report.json    # Latest health report
└── logs/
    └── observation.log       # Observation sweep output
```

### Architecture

The system separates concerns by execution mode:

- **Interactive work** (discovery, interrogation, approval) happens in your normal Claude Code session with you present
- **Automated execution** (deployed agents running workflows) happens headlessly via system cron
- **Monitoring** (reviewing deployed agent health) can run either way

Deployed agents are standalone Claude Code projects, not subagents. Each has its own CLAUDE.md, skills, and configuration. This ensures they are portable, self-contained, and have no runtime dependency on your main agent.

## Customization

### Changing the default agent directory

Deployed agents are created at `~/agents/{agent-name}/` by default. Claudomate asks for confirmation during deployment, so you can choose a different location per agent. To change the default, edit the deployment section in `agents/claudomate.md`.

## Requirements

- Claude Code 1.0.33 or later
- A Claude subscription or Anthropic Console account

## License

MIT
