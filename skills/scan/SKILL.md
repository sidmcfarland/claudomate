---
name: scan
description: >
  Checks the claudomate agent's proposals log and monitoring reports for anything
  that warrants the user's attention. Run at the start of each session.
---

## Working Directory

First, determine `CLAUDOMATE_DIR`:
- If `.claude/claudomate/` exists in the current working directory → use that
- Otherwise → use `~/.claude/claudomate/`

Check the claudomate agent's logs for anything that needs the user's attention.

1. Read `{CLAUDOMATE_DIR}/proposals.json` if it exists. Look for entries with
   status "pending". For each pending proposal, briefly summarize:
   - The workflow name and description
   - The claudomate's confidence level
   - Known gaps that would need to be filled through interrogation

2. Read `{CLAUDOMATE_DIR}/monitoring-report.json` if it exists. Look for any
   critical or warning findings from deployed agents. Summarize:
   - Which agent reported the issue
   - What the issue is
   - Whether it requires user action

3. If there are pending proposals, ask the user if they'd like to explore
   automating any of them. If the user agrees, run the `/claudomate:build`
   skill to begin the workflow modeling process.

4. If there are critical monitoring findings, surface them and recommend next steps.

5. If there is nothing pending and no issues, say so briefly and move on. Do not
   be verbose when there is nothing to report.
