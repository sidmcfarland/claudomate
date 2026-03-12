---
name: stop
description: >
  Pauses claudomate entirely. Disables the session-start hook via the claudomate
  config file and comments out the cron schedules for all claudomate-managed
  agents. Leaves all files and working data intact so claudomate can be fully
  resumed with /claudomate:start.
---

Pause claudomate entirely — no session scanning, no agents running in the
background.

## Working Directory

First, determine `CLAUDOMATE_DIR`:
- If `.claude/claudomate/` exists in the current working directory → use that
- Otherwise → use `~/.claude/claudomate/`

## Steps

### 1. Disable the session-start hook

Read `{CLAUDOMATE_DIR}/config.json` if it exists. Set `"enabled": false`.
If the file doesn't exist, create it with:

```json
{
  "enabled": false
}
```

If it already has `"enabled": false`, note that claudomate is already paused
and continue to step 2.

### 2. Suspend deployed agent cron schedules

Read `{CLAUDOMATE_DIR}/monitoring.json` to get the list of deployed agents
and their directory paths. If the file doesn't exist or has no agents, skip
this step.

For each agent, find its cron entry by running `crontab -l` and looking for a
line that references the agent's directory path. Comment out the line by
prepending `# claudomate-suspended: ` to it. Write the updated crontab back
with `crontab -`.

Example — a cron line like:
```
0 9 * * 1-5 cd {CLAUDOMATE_DIR}/agents/invoice-processor && claude -p --dangerously-skip-permissions
```
becomes:
```
# claudomate-suspended: 0 9 * * 1-5 cd {CLAUDOMATE_DIR}/agents/invoice-processor && claude -p --dangerously-skip-permissions
```

This preserves the full entry so it can be exactly restored by `/claudomate:start`.

### 3. Confirm

Tell the user:
- Whether claudomate was already paused or has just been disabled
- How many agent cron entries were suspended (list agent names)
- That all files, working data, and agent directories are intact
- That `/claudomate:start` will fully resume claudomate and restore all cron schedules
