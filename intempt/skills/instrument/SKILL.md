---
name: instrument
description: Intempt instrument — scan a codebase, build a tracking plan, and generate typed analytics SDK wrappers for 14 platforms. Use for init/generate/add/remove/validate/status, or when the user wants to add analytics tracking to their code.
---

# Intempt instrument

This is the CLI's original, most differentiated capability — the one thing the
console, API, and MCP server genuinely can't do, because it touches the developer's
local filesystem. It's parallel to Amplitude's Ampli/Wizard CLIs, not to a CRM/GTM
tool. Everything here is local-only (works without a resolved org/project beyond
login) and, per its own latency target, should complete in under 5 seconds on a
normal-sized project.

**Important disambiguation:** "event" in this skill means a *tracking-plan event
definition* — a name, properties, and types you're instrumenting in code (`intempt add
signup`). That is a completely different concept from `market`'s live campaign/trigger
events (`intempt events` domain) or a raw ingested event stream. If a user says "add an
event," check which they mean before picking a skill.

## The workflow

```bash
intempt init          # interactive wizard: AI codebase scan, guided, empty, evaluate, or evolve mode
intempt add <event>    # add one event to intempt.yaml interactively
intempt remove <event> # remove an event
intempt validate       # validate intempt.yaml
intempt generate       # generate a typed SDK wrapper or REST client from intempt.yaml, for one of 14 platforms
intempt status [--ci] [--json] [--warn-only]   # check instrumentation coverage
```

`intempt.yaml` is the tracking plan — events, properties, identify/group traits — and
is the sole input to `generate` and the sole thing `add`/`remove`/`validate` operate on.
There's no `--platform` flag for any of these; platform comes from `intempt.yaml` itself.

`init`'s AI-scan mode authenticates as the logged-in user and bills through the
platform like everything else — it does not accept or require a user-supplied LLM
provider key of any kind.

## Known limitations — don't overstate what these do

- **`status`'s coverage scanner only has real patterns for 6 of 14 platforms** — the
  other 8 fall back to a generic pattern. Don't present a clean `status` result for one
  of those 8 as strong evidence of full coverage.
- **`intempt push` exists but doesn't do what its own name implies.** It POSTs directly
  to a schema endpoint with no diff display, no confirmation prompt, and no `--force`
  flag — not the safe, reviewable sync its spec calls for. Warn the user before they
  rely on it to safely sync a large tracking plan.
- **`intempt pull` and `intempt diff` don't exist at all** — no command, no file, in the
  shipped CLI. Don't imply either is available as a safer alternative to `push`.
- **SDK install-command output is incomplete for 8 of 14 platforms** — `generate` may
  print nothing after codegen for those.

## After generating

Every platform's typed wrapper is meant to be committed alongside the tracking plan —
this is genuinely local codegen, not something synced from the platform. `intempt.yaml`
supports a `version:` field; newer CLI versions auto-migrate older ones, older CLIs
reject newer versions with an upgrade message.
