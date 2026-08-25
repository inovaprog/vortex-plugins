# Vortex plugins

Official plugins for using Vortex from AI coding agents without storing a
project API key in a repository.

## Claude Code

```text
/plugin marketplace add inovaprog/vortex-plugins
/plugin install vortex@vortex-plugins
```

## Codex

```sh
codex plugin marketplace add inovaprog/vortex-plugins --ref main
codex plugin add vortex@vortex-plugins
```

The plugin starts `vortex mcp serve` locally; it uses the session created by
`vortex login` and the repository binding in `.vortex/config.json`.

## Execute a task

Ask the agent to **“executar com Vortex ABC-1234”**. In Claude Code, the
equivalent explicit command is:

```text
/vortex-executar ABC-1234
```

Both start one visible workflow: inspect the task and repository, refine with
the best available reasoning capability, ask a question only when an essential
decision is missing, mark the task in progress, implement, verify, open a PR,
record the local execution in the project's `ai_context`, and move it to
review. The older `/vortex-refinar` intent remains available for refinement-only
work.

Automatic pre-refinement is optional context. It never blocks execution,
changes status, or creates a question; questions arise only from the real
refinement step when implementation cannot safely proceed.

## First use

The plugin checks `vortex doctor --json`. If the CLI is absent, the agent asks
before running `npm install --global @inovaprog/vrtex@latest`; then it guides
the user through browser login and `vortex init --skip-tests` when required.
Passwords, tokens, and `X-Project-Key` are never requested or committed.
