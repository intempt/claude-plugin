# Intempt Claude Code Plugin

Installs the Intempt CLI and MCP server for Claude Code in two commands, with a
bundled setup skill that handles login and org/project selection.

## Install

```
/plugin marketplace add intempt/claude-plugin
/plugin install intempt@intempt-plugins
```

Restart Claude Code (exit and relaunch) so the newly-installed MCP server is
picked up, then say:

```
run intempt setup
```

This invokes the bundled `intempt:setup` skill, which:

1. Installs `@intempt/cli` globally via npm if it isn't already on `PATH`
2. Runs `intempt login` (opens your browser)
3. Runs `intempt use` if your account belongs to more than one org/project
4. Verifies both the CLI and the MCP server's tools are working

## What's in this repo

| Path | What it is |
|------|------------|
| `.claude-plugin/marketplace.json` | Marketplace manifest (what `/plugin marketplace add` reads) |
| `intempt/.claude-plugin/plugin.json` | Plugin manifest |
| `intempt/.mcp.json` | Registers the MCP server (`npx -y @intempt/mcp-server`) |
| `intempt/skills/setup/SKILL.md` | The `intempt:setup` skill |

The actual CLI and MCP server live in [intempt/cli](https://github.com/intempt/cli)
and are published to npm as `@intempt/cli` and `@intempt/mcp-server`. This repo
is only the Claude Code distribution wrapper around those published packages --
it has no source code of its own.

## License

MIT
