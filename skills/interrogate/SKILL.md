---
name: interrogate
description: >
  Invokes the claudomate agent as a foreground subagent to interactively model
  a workflow with the user. Use when the user wants to explore automating a
  specific workflow.
---

Invoke the claudomate subagent in the foreground to begin or continue modeling a
workflow for automation.

If the user has specified which workflow to model, pass that context to the
claudomate agent. If not, the claudomate agent should present the pending proposals
and let the user choose.

The claudomate agent will:
- Review what is already known about the workflow
- Ask the user targeted questions to fill in the gaps
- Build a complete workflow model
- Present the finished specification for approval
- If approved, deploy a self-contained agent to execute the workflow on schedule

This process may span multiple sessions. The claudomate agent saves its progress
between sessions and will resume where it left off.
