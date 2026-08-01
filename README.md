# Intempt Claude Code Plugin

Registers the Intempt MCP server and bundles a setup skill that installs the
CLI, logs in, and resolves org/project -- so you don't have to do any of that
by hand.

## Install

```
/plugin marketplace add intempt/claude-plugin
/plugin install intempt@intempt-plugins
```

Restart Claude Code (exit and relaunch) -- the MCP server this plugin
registers isn't picked up until the next session start. Then say:

```
run intempt setup
```

This invokes the bundled `intempt:setup` skill, which:

1. Installs `@intempt/cli` globally via npm if it isn't already on `PATH`
2. Logs you in (prefers the MCP server's own `login` tool; falls back to
   `intempt login`, which opens your browser)
3. Runs `intempt use --org ... --project ...` non-interactively if your
   account belongs to more than one org, or one org with more than one
   project
4. Verifies both the CLI and the MCP server's tools are working

## What's in this repo

| Path | What it is |
|------|------------|
| `.claude-plugin/marketplace.json` | Marketplace manifest (what `/plugin marketplace add` reads) |
| `intempt/.claude-plugin/plugin.json` | Plugin manifest |
| `intempt/.mcp.json` | Registers the MCP server (`npx -y @intempt/mcp-server`) |
| `intempt/skills/setup/SKILL.md` | The `intempt:setup` skill |

The actual CLI and MCP server are built in Intempt's internal monorepo and
published to npm as `@intempt/cli` and `@intempt/mcp-server`. This repo is
only the Claude Code distribution wrapper around those published packages --
it has no source code of its own.

## License

MIT
