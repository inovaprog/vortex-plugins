---
name: vortex
description: Use Vortex tasks and project context from the authenticated Vortex CLI.
---

Use `vortex doctor --json` before operating Vortex.

If the CLI is missing, ask approval before running
`npm install --global @inovaprog/vrtex@latest`. If authentication is missing,
run `vortex login`; this opens the secure browser login and never
requires the user to provide a password or token in chat. If the repository is
not linked, run `vortex init --skip-tests` and let the user select the project.

Use the Vortex MCP tools once ready. For transparent terminal workflows, use
`vortex task list`, `vortex task context ABC-1234 --json`, and `vortex task
create`. State the project and task ID before a mutation; never change task
status without the user's request.
