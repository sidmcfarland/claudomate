---
name: start
description: >
  Activates claudomate. Enables the session-start scan hook via the claudomate
  config file and restores any suspended agent cron schedules. Safe to run
  multiple times — skips steps that are already in the correct state.
---

Activate or resume claudomate — session scanning on, all agents running.

## Steps

### 1. Enable the session-start hook

Read `~/.claude/claudomate/config.json` if it exists. Set `"enabled": true`.
If the file doesn't exist, create it with:

```json
{
  "enabled": true
}
```

If it already has `"enabled": true`, note that claudomate is already active
and continue to step 2.

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
- Whether claudomate was already active or has just been enabled
- How many agent cron schedules were restored (list agent names if any)
- That the session-start scan will now run automatically at the start of each new session
- That `/claudomate:scan` can also be run manually at any time
