---
name: vortex
description: Use when the user asks to create, search, refine, update, close, or execute a Vortex task; phrases such as "executar com Vortex" start the unified task-delivery workflow.
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
vortex task update ABC-1234 --status progresso
```

Always state the linked project and readable task ID before changing a task.
Do not close a task, alter its status, or create a task unless the user asked.

## Unified delivery workflow

Treat “executar com Vortex”, “execute this Vortex task”, and the legacy
`/vortex-executar` intent as the same request. Do not make the user choose a
separate “refine” versus “execute” command. Announce each step briefly so the
user knows whether work is progressing or waiting on them.

1. **Locate and inspect.** Confirm the local project binding, then call
   `get_task_context` (or `vortex task context ABC-1234 --json`). Read the
   task, attachments available in its context, acceptance criteria, existing
   plan, and repository files before proposing changes. Pre-refinement is
   optional context only: never wait for it, requeue it, or use it as a gate.
   **Check eligibility before anything else:** start delivery automatically
   only from `aberto`. A task in `progresso` may be resumed only when the user
   explicitly asked to resume it and inspection shows delivery is incomplete.
   For `review`, `fechado`, `arquivado`, `duvida`, or any other status, do not
   edit files, change status, reopen the task, or create another PR. Report
   its current state and ask the user to explicitly choose a new action. A
   `review` task is presumed delivered even if the current checkout lacks its
   branch.
2. **Refine with quality.** Use the strongest model/capability available to
   reason about the request. Produce a concise implementation plan, affected
   areas/files, acceptance criteria, risks, and focused checks. Persist the
   plan with the Vortex MCP `set_task_plan` tool when available. Do not start
   editing until this pass is complete.
3. **Ask only indispensable questions.** If a missing decision makes a safe
   plan impossible, use `set_refinement_question` with one specific question,
   set the task to `duvida`, explain that execution is waiting, and stop.
   Never create a doubt from automatic pre-refinement, task size, or a generic
   preference. If a reasonable implementation choice exists, state it and
   continue.
4. **Make progress visible.** Immediately before implementation, set status
   to `progresso` using `update_task_status` (or `vortex task update`). Never
   claim progress before this point and never mark a task complete while work
   or verification is still pending.
5. **Implement economically.** Execute the approved plan in the local
   repository. Use the configured execution-capable model/agent for code
   changes; reserve the high-quality reasoning pass for planning and review.
   Keep the change atomic and avoid unrelated refactors.
6. **Verify, record, and deliver.** Run proportional, focused checks. Report
   failures honestly; leave the task `progresso` if they block delivery. On
   success, create a branch, commit, push, and open a PR. Then record exactly
   one local execution before changing the final status:

   ```sh
   vortex task report-execution ABC-1234 --status success \
     --pr-url "https://github.com/owner/repo/pull/123" \
     --summary "What changed and which checks passed." --model codex
   ```

   On failure, use `--status error --summary "What was attempted." --error
   "Blocking failure"` and keep `progresso`. This command creates execution
   telemetry and updates the project's `ai_context` for future tasks; it does
   not replace a task result or change task status. After a successful record,
   store the PR URL/result with MCP `update_task` when available, then set
   status to `review`. Do not close the task merely because a PR was opened.

For a request that only says “refinar”, perform steps 1–3 and wait for the
user before implementation. For a request that only asks to inspect, list, or
edit a task, do only that requested action.

The legacy `/vortex-refinar` intent is compatible as “refine only”; the legacy
`/vortex-executar` intent starts the full workflow above. Do not depend on old
`.mcp.json` or `X-Project-Key` flows.
