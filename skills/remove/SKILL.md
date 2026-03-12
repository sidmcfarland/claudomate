---
name: remove
description: >
  Fully uninstalls claudomate. Removes the session-start hook from CLAUDE.md,
  kills all deployed agents (cron entries + directories), and deletes all
  claudomate working files. Ends with instructions for uninstalling the plugin
  itself.
---

Fully uninstall claudomate from this machine.

## Steps

### 1. Confirm intent

This is a destructive, multi-step operation. Before doing anything, tell the user
what will be removed and ask for explicit confirmation:

```
This will permanently remove:
- The claudomate session-start hook from CLAUDE.md
- All deployed agents (cron entries + project directories)
- All claudomate working files (~/.claude/claudomate/)

Deployed agents and their history cannot be recovered after this.
Continue? (yes/no)
```

If the user says no, stop immediately.

### 2. Remove the session-start hook

Check both `CLAUDE.md` in the current working directory and `~/.claude/CLAUDE.md`
for the following line and remove it from any file where it appears:

```
At the start of each new session or conversation, run the `/claudomate:scan` skill.
```

### 3. Remove all deployed agents

Read `~/.claude/claudomate/monitoring.json` to get the list of deployed agents.
For each agent:

a. Remove its cron entry: run `crontab -l`, filter out any line that references
   this agent's directory — whether active or suspended (i.e., prefixed with
   `# claudomate-suspended: `). Write the result back with `crontab -`. Be
   careful not to remove unrelated cron entries.

b. Delete its project directory with `rm -rf {path}`. Verify the path is from
   monitoring.json and is within the home directory before deleting.

If monitoring.json doesn't exist or has no agents, skip this step.

### 4. Remove claudomate working files

Delete the entire claudomate working directory:

```bash
rm -rf ~/.claude/claudomate/
```

### 5. Instruct the user to uninstall the plugin

Tell the user the automated cleanup is complete, then instruct them to run the
following command to remove the plugin itself (this cannot be done from within
a skill):

```
/plugin uninstall claudomate@sidmcfarland
```

If the plugin was installed locally (via `--plugin-dir`), they should simply
stop passing that flag when launching Claude Code.

### 6. Final confirmation

Summarize what was removed: hook location(s), number of agents killed, working
files deleted. Remind the user to run the uninstall command above.
