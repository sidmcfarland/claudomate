# Claudomate

A Claude Code plugin that discovers your repetitive workflows and deploys autonomous agents to handle them for you.

## What it does

Claudomate is a subagent that progressively automates your routine tasks. It works in four phases:

1. **Observe** — Scans your agent's memory and session history for repetitive patterns
2. **Propose** — Surfaces candidate workflows and asks if you'd like them automated
3. **Interrogate** — Asks targeted questions to fully model a workflow's steps, tools, edge cases, and schedule
4. **Deploy** — Creates a self-contained Claude Code project with its own CLAUDE.md, skills, and cron schedule to execute the workflow autonomously

Over time, claudomate builds a growing ecosystem of purpose-built agents that handle your routine work so you can focus on tasks that genuinely require your judgment.

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

### Step 2: Add the session-start check to your CLAUDE.md

This step is required. Without it, claudomate has no way to surface proposals
and monitoring alerts to you at the start of each session.

Open your project's `CLAUDE.md` file (or `~/.claude/CLAUDE.md` if you want this
active across all projects). Add the following line:

```
At the start of each new session or conversation, run the `/claudomate:check-claudomate` skill.
```

If the file does not exist, create it with that line as its contents.

This tells your main agent to check for pending automation proposals and deployed
agent health alerts whenever you start a new session or conversation.

## Uninstallation

### Step 1: Remove the session-start check from your CLAUDE.md

Open the `CLAUDE.md` file where you added the line during installation and remove:

```
At the start of each new session or conversation, run the `/claudomate:check-claudomate` skill.
```

### Step 2: Uninstall the plugin

```bash
/plugin uninstall claudomate@sidmcfarland
```

### Step 3 (optional): Remove claudomate working files

Claudomate stores proposals, workflow models, and monitoring data at
`~/.claude/claudomate/`. If you no longer need this data:

```bash
rm -rf ~/.claude/claudomate/
```

### Step 4 (optional): Remove deployed agents

Any agents claudomate deployed are standalone projects in their own
directories (default: `~/agents/{agent-name}/`). Each also has a system cron
entry. To fully remove a deployed agent:

1. Remove its cron entry: `crontab -e` and delete the relevant line
2. Delete its project directory: `rm -rf ~/agents/{agent-name}/`

Repeat for each deployed agent. You can find the full list of deployed agents
in `~/.claude/claudomate/monitoring.json` (if it still exists) or by checking
your crontab.

## Usage

### Automatic discovery

Once installed, claudomate can be invoked to scan for automatable workflows:

```
Scan my workflows for anything that could be automated.
```

It reads your agent's memory for patterns — things you do repeatedly, on a schedule, or that you've mentioned as routine tasks. Candidates are written to a proposals log.

### Reviewing proposals

At the start of each session (if you added the CLAUDE.md line), your agent checks for pending proposals and summarizes them. You can also check manually:

```
/claudomate:check-claudomate
```

### Modeling a workflow

When you approve a proposal, start the interactive modeling process:

```
/claudomate:interrogate
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
| `skills/check-claudomate/` | Session-start skill that reads proposals and monitoring logs |
| `skills/interrogate/` | Skill that invokes claudomate for interactive workflow modeling |

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

### Renaming the plugin

To use a different name:

1. Update the `name` field in `.claude-plugin/plugin.json`
2. Update the `name` field in `agents/claudomate.md` frontmatter
3. Update references to `claudomate` in the skill files (the `/claudomate:` namespace prefix changes automatically to match the plugin name)
4. Update the working directory path `~/.claude/claudomate/` in the agent prompt to `~/.claude/{your-name}/`

### Changing the default agent directory

Deployed agents are created at `~/agents/{agent-name}/` by default. Claudomate asks for confirmation during deployment, so you can choose a different location per agent. To change the default, edit the deployment section in `agents/claudomate.md`.

## Requirements

- Claude Code 1.0.33 or later
- A Claude subscription or Anthropic Console account

## License

MIT
