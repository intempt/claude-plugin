---
name: setup
description: Intempt setup -- install the intempt CLI, log in, and resolve org/project. Use when `intempt` is not found on PATH, `intempt whoami` fails, the Intempt MCP tools error on auth, or the user asks to set up/configure Intempt.
---

# Intempt setup

Two things need to work: the `intempt` CLI (on PATH, signed in) and the
Intempt MCP server (already registered by this plugin's `.mcp.json` --
nothing to do there beyond the one restart in step 1 if you just installed
the plugin).

## 1. Restart check (only if the plugin was just installed)

If the plugin was installed in *this* session, Claude Code has not yet
spawned the `intempt` MCP server -- it only does that at session start.
Check with:

```bash
claude mcp list
```

If `intempt` is missing from the list, tell the user to exit and restart
Claude Code, then continue from step 2. If it's already listed (Connected or
Needs authentication), no restart is needed yet.

## 2. Check whether the CLI is installed

```bash
command -v intempt && intempt --version
```

- **Found** -- skip to step 3.
- **Not found** -- install it:

  ```bash
  npm install -g @intempt/cli
  ```

  Then re-run `command -v intempt` to confirm it's on PATH. If it's still
  not found after a successful install, npm's global bin directory isn't on
  the user's PATH -- tell them to add it (`npm config get prefix` shows the
  prefix; the bin directory is `<prefix>/bin`) and open a new shell.

## 3. Check login state

```bash
intempt whoami; echo "exit_code=$?"
```

- **exit_code=0** -- already logged in. The printed output lists every org
  the account belongs to (and projects within each). If it lists **more than
  one organization**, go to step 5 (org/project resolution) even though
  login itself is done. If it lists exactly one org and the account has
  a project resolved automatically (login already picked it when
  unambiguous), skip to step 6.
- **exit_code=1** ("Not logged in") -- go to step 4.

## 4. Log in

```bash
intempt login
```

This opens a browser automatically (device-code flow) and blocks until the
user approves. No restart is needed afterward for the CLI -- every `intempt`
invocation is a fresh process that reads the current session from
`~/.intempt/auth.json`.

**If the Intempt MCP tools (not just the CLI) are erroring on auth after this
login**, that's the one case that *does* need a restart: the MCP server is a
long-running process that only reads that file once, at its own startup. It
picks up a session created by a *separate* CLI login only after Claude Code
restarts. (If the user instead logs in by asking Claude to use the MCP
server's own `login` tool, no restart is needed at all -- that login happens
inside the already-running MCP process and updates its in-memory session
directly.)

After login, re-run `intempt whoami` and continue at step 3's org-count logic.

## 5. Resolve org/project (only if whoami listed more than one organization)

`intempt login` already auto-picks the default org/project when the account
is unambiguous (exactly one org, exactly one project in it) -- you only
land here for a genuinely multi-org or multi-project account, where guessing
would be wrong.

Ask the user which org (and, if that org has more than one project, which
project) they want to use -- list the exact names from `intempt whoami`'s
output. Then set it non-interactively (don't run bare `intempt use` here --
its interactive picker needs a real terminal, which a tool call doesn't have):

```bash
intempt use --org "<chosen org name>" --project "<chosen project name>"
```

Omit `--project` if the chosen org has only one project (it'll be filled in
automatically) or if the user wants to decide that later.

## 6. Verify both surfaces

```bash
intempt whoami
```

Confirm it prints "Logged in as ..." with the org/project the user expects.

Then confirm the MCP tools work -- call any Intempt MCP tool (e.g. ask a
`whoami`-equivalent question, or list something read-only) and confirm it
succeeds rather than erroring with an auth/org message. If it errors with
"Organization and project are required" specifically, that means
`INTEMPT_ORG`/`INTEMPT_PROJECT` env vars are unset (fine, expected) *and* the
stored default from step 5 isn't visible yet -- if the MCP server was
already running before step 5's `intempt use` call, this is the same
restart case called out in step 4: the running server only reads
`~/.intempt/auth.json` at its own startup.

Setup is complete once `intempt whoami` succeeds and the MCP tools work
without an org/project error.
