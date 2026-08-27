# opencode

opencode has no plugin marketplace; it consumes plain skill files and its own
MCP configuration. The Vortex skill ships in this repo at
`plugins/vortex/skills/vortex/SKILL.md` and can be used by opencode without
any conversion — the frontmatter format is the same.

## Global install

Copy the skill to the opencode global skills directory:

```sh
mkdir -p ~/.config/opencode/skills
cp -r plugins/vortex/skills/vortex ~/.config/opencode/skills/vortex
```

This makes the skill available in every repository. It stays quiet until the
user asks for a Vortex task operation ("executar com Vortex", "criar tarefa",
"refinar", and so on), exactly like the Codex/Claude plugins.

## MCP server

The skill calls MCP tools (`get_task_context`, `set_task_plan`,
`update_task_status`, ...) through the local bridge that runs
`vortex mcp serve`. Register it in the opencode configuration
(`~/.config/opencode/opencode.json` for global, or `opencode.json` in the
repository for project scope):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "vortex": {
      "type": "local",
      "command": ["vortex", "mcp", "serve"]
    }
  }
}
```

The bridge uses the session created by `vortex login` and the repository
binding in `.vortex/config.json`; it never stores or reads a project API key.

## Restart

opencode loads configuration and skills once at startup. After installing the
skill or editing `opencode.json`, quit and restart opencode for the changes to
take effect.

## Project scope instead

To scope the skill and MCP server to a single repository, place them at
`.opencode/skills/vortex/SKILL.md` and merge the `mcp` block above into that
repository's `opencode.json`.

## First use

The skill checks `vortex doctor --json`. If the CLI is absent, the agent asks
before running `npm install --global @inovaprog/vrtex@latest`; then it guides
the user through browser login and `vortex init --skip-tests` when required.
Passwords, tokens, and `X-Project-Key` are never requested or committed.
