# Changelog

## 2026-08-01

- **Added:** initial release. Marketplace manifest, `intempt` plugin (registers the MCP server as `npx -y @intempt/mcp-server@^1.0.0`, no bundled binary since the packages publish to public npm), and the bundled `intempt:setup` skill (installs the CLI via npm if missing, logs in, resolves org/project non-interactively when ambiguous, verifies both surfaces).
- **Fixed** (pre-merge, adversarial review): the setup skill's restart check used `claude mcp list`, a separate process reading on-disk config that can't reflect whether *this* session's tools are actually live -- replaced with a direct tool-reachability check.
- **Fixed:** the skill's org/project gating only checked org count, missing the single-org/multiple-project case entirely (`pickDefaultOrgProject` leaves the project unset there too).
- **Fixed:** the skill's verification step asked for something `intempt whoami` can't show (the currently-selected default, vs. the full membership tree it actually prints) -- rewritten to use `intempt use`'s own confirmation line and the MCP `whoami` tool's Organization/Project fields.
- **Fixed:** the skill treated `intempt whoami`'s exit code 1 as always meaning "not logged in"; it also covers network/API failures, which need a different response.
- **Changed:** the skill now prefers the MCP server's own `login` tool over the CLI's `intempt login` in a Bash call, which blocks until browser approval (can exceed a tool-call timeout) and prints no fallback link in headless/SSH contexts.
