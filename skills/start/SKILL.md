---
name: start
description: >
  Activates claudomate. Adds the session-start scan hook to CLAUDE.md and
  restores any suspended agent cron schedules. Creates CLAUDE.md if it doesn't
  exist. Safe to run multiple times — skips steps that are already in the
  correct state.
---

Activate or resume claudomate — session scanning on, all agents running.

## Steps

### 1. Add the session-start hook

Ask the user whether to activate claudomate for:
- **This project only** — add to `CLAUDE.md` in the current working directory
- **All projects (global)** — add to `~/.claude/CLAUDE.md`

Check whether the target CLAUDE.md already contains the scan line:
```
At the start of each new session or conversation, run the `/claudomate:scan` skill.
```

If the line is already present, note that the hook is already active and continue
to step 2. Do not add a duplicate.

If the file exists but doesn't have the line, append it to the end of the file.

If the file doesn't exist, create it with that line as its only content.

### 2. Restore suspended agent cron schedules

Run `crontab -l` and look for any lines beginning with
`# claudomate-suspended: `. For each one found, restore it by removing that
prefix, leaving the original cron entry uncommented. Write the updated crontab
back with `crontab -`.

Example — a suspended line like:
```
# claudomate-suspended: 0 9 * * 1-5 cd ~/agents/invoice-processor && claude -p --dangerously-skip-permissions
```
becomes:
```
0 9 * * 1-5 cd ~/agents/invoice-processor && claude -p --dangerously-skip-permissions
```

If no suspended entries are found, skip this step silently.

### 3. Confirm

Tell the user:
- Whether the session-start hook was added or was already present, and in which file
- How many agent cron schedules were restored (list agent names if any)
- That `/claudomate:scan` will now run automatically at the start of each new session
- That `/claudomate:scan` can also be run manually at any time
