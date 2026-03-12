---
name: kill
description: >
  Removes a specific deployed agent. Deletes its cron entry, project directory,
  and removes it from the claudomate agent registry. Use when you want to
  permanently retire one automated workflow.
---

Remove a specific deployed agent managed by claudomate.

## Steps

1. Read `~/.claude/claudomate/monitoring.json`. This file contains the registry
   of deployed agents. If the file doesn't exist or is empty, tell the user
   there are no deployed agents to remove and stop.

2. List the deployed agents with their names and descriptions so the user can
   choose which one to remove. If there is only one agent, confirm with the user
   before proceeding.

3. Once the user selects an agent, confirm the action — this is destructive and
   cannot be undone:
   ```
   Remove agent "{name}"?
   - Cron entry: {schedule}
   - Directory: {path}
   This will permanently delete the agent and all its files. Confirm? (yes/no)
   ```

4. Remove the agent's cron entry by running `crontab -l`, filtering out any line
   that references this agent's directory — whether active or suspended (i.e.,
   prefixed with `# claudomate-suspended: `). Write the result back with
   `crontab -`. Be careful not to remove unrelated cron entries.

5. Delete the agent's project directory using `rm -rf {path}`. Double-check that
   the path comes from monitoring.json and is inside a safe location (e.g. home
   directory) before deleting.

6. Remove the agent's entry from `~/.claude/claudomate/monitoring.json` and save
   the updated file.

7. Confirm to the user that the agent has been fully removed: cron entry deleted,
   directory removed, registry updated.
