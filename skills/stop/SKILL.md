---
name: stop
description: >
  Pauses claudomate entirely. Removes the session-start hook from CLAUDE.md and
  comments out the cron schedules for all claudomate-managed agents. Leaves all
  files and working data intact so claudomate can be fully resumed with
  /claudomate:start.
---

Pause claudomate entirely — no session scanning, no agents running in the
background.

## Steps

### 1. Remove the session-start hook

Check both possible locations for the claudomate scan line:
- `CLAUDE.md` in the current working directory (project scope)
- `~/.claude/CLAUDE.md` (global scope)

The line to look for is:
```
At the start of each new session or conversation, run the `/claudomate:scan` skill.
```

For each file where the line is found, remove it. If the line is the only content
in the file, ask the user whether to delete the file or leave it empty. Do not
modify anything else in the file.

If the line is not found in either location, note this and continue — do not stop.

### 2. Suspend deployed agent cron schedules

Read `~/.claude/claudomate/monitoring.json` to get the list of deployed agents
and their directory paths. If the file doesn't exist or has no agents, skip
this step.

For each agent, find its cron entry by running `crontab -l` and looking for a
line that references the agent's directory path. Comment out the line by
prepending `# claudomate-suspended: ` to it. Write the updated crontab back
with `crontab -`.

Example — a cron line like:
```
0 9 * * 1-5 cd ~/agents/invoice-processor && claude -p --dangerously-skip-permissions
```
becomes:
```
# claudomate-suspended: 0 9 * * 1-5 cd ~/agents/invoice-processor && claude -p --dangerously-skip-permissions
```

This preserves the full entry so it can be exactly restored by `/claudomate:start`.

### 3. Confirm

Tell the user:
- Whether the session-start hook was removed and from which file(s)
- How many agent cron entries were suspended (list agent names)
- That all files, working data, and agent directories are intact
- That `/claudomate:start` will fully resume claudomate and restore all cron schedules
