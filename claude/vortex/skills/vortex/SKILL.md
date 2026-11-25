---
name: vortex
description: Use Vortex tasks and project context from the authenticated Vortex CLI. “Executar com Vortex” and /vortex-executar trigger the unified delivery workflow.
---

Use `vortex doctor --json` before operating Vortex.

If the CLI is missing, ask approval before running
`npm install --global @inovaprog/vrtex@latest`. If authentication is missing,
run `vortex login`; this opens the secure browser login and never requires the
user to provide a password or token in chat. If the repository is not linked,
run `vortex init --skip-tests` and let the user select the project.

Use the Vortex MCP tools once ready. For transparent terminal workflows, use
`vortex task list`, `vortex task context ABC-1234 --json`, and `vortex task
create`. State the project and task ID before a mutation; never change task
status without the user's request.

## Unified delivery workflow

Treat “executar com Vortex” and `/vortex-executar` as one workflow. Do not ask
the user to choose a separate refinement command. Announce each stage briefly:

Resolve the delivery base from the branch configured in the Vortex project's
GitHub settings (`github_ref`). If the project has no configured reference
branch, use the Git repository's default remote branch. Confirm the base before
editing. For multiple tasks, follow the
[stacked PR workflow](#delivering-multiple-tasks-as-stacked-prs).

### Model reference for refinement and execution
When executing the workflow, follow this reference to select the correct model for refinement (stronger model) and execution (economical model):
- **Codex**: refinement (stronger model) -> **Terra** or **Sol** / execution (weaker model) -> **Lua** or **Terra**
- **Claude**: refinement (stronger model) -> **Opus** / execution (weaker model) -> **Sonnet**
- **Gemini & DeepSeek**: refinement (stronger model) -> **Pro** / execution (weaker model) -> **Flash**

1. Confirm the linked project and inspect the task with `get_task_context` or
   `vortex task context ABC-1234 --json`, including attachments, acceptance
   criteria, existing plan, and relevant repository files. Pre-refinement is
   context only and never blocks this step.
   Before any mutation, check eligibility: start delivery automatically only
   from `aberto`. A task in `progresso` may be resumed only when the user
   explicitly asked to resume it and inspection shows delivery is incomplete.
   For `review`, `fechado`, `arquivado`, `duvida`, or any other status, do not
   edit files, change status, reopen the task, or create another PR. Report
   its current state and ask the user to explicitly choose a new action. A
   `review` task is presumed delivered even if the current checkout lacks its
   branch.
2. Refine before coding with the strongest capability available (the designated
   refinement models are **Terra** or **Sol** for Codex, **Opus** for Claude, and
   **Pro** for Gemini and DeepSeek): write a short plan, affected areas/files,
   acceptance criteria, risks, and focused checks. Persist it with MCP
   `set_task_plan` when available.
3. Only if a missing decision makes a safe plan impossible, use
   `set_refinement_question` for one objective question, set `duvida`, explain
   the wait, and stop. Never create a doubt from pre-refinement or task size.
4. Before editing, set `progresso` with `update_task_status` (or `vortex task
   update`). Create a task branch from its chosen base and implement the plan
   economically and atomically (the
   designated execution models are **Lua** or **Terra** for Codex, **Sonnet**
   for Claude, and **Flash** for Gemini and DeepSeek).
5. Run proportional verification. If blocked, record one execution with
   `vortex task report-execution ABC-1234 --status error --summary "What was
   attempted." --error "Blocking failure"` and keep `progresso`. If successful,
   commit, push, and open a PR against the base used to create the task branch.
   Record exactly one local execution before moving the task:

   ```sh
   vortex task report-execution ABC-1234 --status success \
     --pr-url "https://github.com/owner/repo/pull/123" \
     --summary "What changed and which checks passed." --model claude
   ```

   This saves a durable execution entry and updates the project's `ai_context`
   for future task refinement. It does not itself change the task status. Save
   the PR/result with MCP `update_task` where available, then move the task to
   `review`; do not close it merely because a PR exists.

`/vortex-refinar` remains a compatible refine-only intent: perform steps 1–3
and wait before coding. `/vortex-executar` starts the full workflow. Use only
the authenticated CLI and binding; never use `.mcp.json` with `X-Project-Key`.

### Delivering multiple tasks as stacked PRs

1. Refine each task separately. Order prerequisites before their dependents and
   use the requested order for tasks without dependencies. Include the order
   and intended base branches in the plan before editing.
2. Use one branch and one PR per task. Create the first branch from the latest
   remote tip of the Vortex project's reference branch and target that same
   reference branch in the first PR. Create each later branch from the head of
   the previous PR and target that head branch in its PR.
3. Verify each task on its branch, push it, and open its PR with an explicit
   base (for example, `gh pr create --base <base-branch> --head <task-branch>`).
   Check the diff against that base so the PR shows only this task's changes.
   Link the preceding PR in the description and record this PR's URL, checks,
   execution result, and review status on its own Vortex task. Open the next
   PR after this delivery succeeds. If a task is blocked, keep the PRs already
   delivered available for review and pause the rest of the stack.
4. When merges are authorized, merge from the bottom of the stack upward.
   Record the parent branch's tip before merging. After a parent merges,
   update the next branch and retarget its PR to the parent's base. For a
   squash or rebase merge, use the parent's recorded tip to replay only
   the child's commits onto the new base. Restack descendants in order and
   verify the new diffs and CI before merging again.
