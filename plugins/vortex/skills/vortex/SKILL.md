---
name: vortex
description: Use when the user asks to create, search, refine, update, or close a Vortex task, or asks for Vortex project context.
---

# Vortex task workflow

Use the local Vortex CLI and its authenticated MCP bridge. Never request, read,
store, or commit a Vortex project API key.

Before a Vortex action, run `vortex doctor --json`.

- If `vortex` is unavailable, explain that the CLI must be installed and ask
  for confirmation before running `npm install --global @inovaprog/vrtex@latest`.
- If the user is not authenticated, run `vortex login`; it opens the Vortex
  browser sign-in flow. Never ask for a password or token in chat.
- If no `.vortex/config.json` exists, run `vortex init --skip-tests` in the
  repository and let the user choose the accessible project.

Once ready, prefer the MCP tools for structured task operations. Equivalent CLI
commands are useful for transparent terminal work:

```sh
vortex task list
vortex task context ABC-1234 --json
vortex task create "Title" --description "..."
vortex task close ABC-1234
```

Always state the linked project and readable task ID before changing a task.
Do not close a task, alter its status, or create a task unless the user asked.
