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

## First use

The plugin checks `vortex doctor --json`. If the CLI is absent, the agent asks
before running `npm install --global @inovaprog/vrtex@latest`; then it guides
the user through browser login and `vortex init --skip-tests` when required.
Passwords, tokens, and `X-Project-Key` are never requested or committed.
