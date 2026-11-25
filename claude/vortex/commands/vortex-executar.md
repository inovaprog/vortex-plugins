---
description: Execute one or more Vortex tasks through the unified refinement and delivery workflow.
argument-hint: "<TASK-ID> [TASK-ID ...]"
---

Execute the requested Vortex tasks using the Vortex skill's **Unified delivery
workflow**. Treat `$ARGUMENTS` as the readable task IDs. Run the readiness
checks first, announce each stage, inspect and refine before editing, then
implement, verify, open each task's PR, and move the delivered task to review.
For multiple tasks, follow **Delivering multiple tasks as stacked PRs**.
Pre-refinement is optional context.
