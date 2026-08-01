---
name: registry
description: Intempt registry — discover and call any of the 202 platform-operation commands directly, across both the CLI and MCP tools. Use when a task doesn't fit crm/analyze/design/market/sell, when you need a command not covered by those skills, or to look up an entry's exact arguments.
---

# Intempt registry

`@intempt/commands` is the single shared registry behind both the CLI and the MCP
tools — 202 entries across 13 feature domains, each with a name, arguments, REST
endpoint, and a write-safety class. Neither surface wraps the other; both render the
same registry independently. This skill is the general-purpose way to reach any of it
directly — the domain skills (`crm`, `analyze`, `design`, `market`, `sell`) are curated
paths through the parts of this same registry that map to a real customer job. Use this
skill when your task doesn't fit one of those, or you need an entry by exact name.

## Discovering what's available

```bash
intempt registry list [--json]              # all 202 entries, grouped by domain
intempt registry describe <tool-name> [--json]   # one entry's args, resolvers, endpoint
```

Via MCP, just ask — the tool descriptions are generated from the same registry, so
listing available tools serves the same purpose.

## Calling an entry directly

CLI: `intempt <domain> <action> [--flags]` — e.g. `intempt users get_user_overview --email sarah@acme.com`, `intempt segments list_segments`. `<domain>` and `<action>` are the registry entry's literal `domain`/`name` fields, used verbatim — no shortening, no prefix-stripping. Entries with no domain (only `search_project`, a confirmed stub) render at the top level: `intempt search_project`.

MCP: the equivalent tool is called directly by name (111 of the 156 MCP-eligible entries
are consolidated into 16 view-parameterized tools — e.g. `get_account_detail` replaces
6 separate account-detail reads).

Both accept `--json`/return raw JSON for machine consumption.

## Natural-language identifier resolution

Any `{id}`-parameterized `users`/`accounts` entry accepts a name or email instead of an
internal ID — it resolves via `POST /profile-lists/{users|accounts}`, first match, when
no explicit ID is passed. **There is no disambiguation on multiple matches** — if two
people share a name, you get whichever the endpoint returns first, silently. If a lookup
result looks wrong, consider that a same-name collision before assuming the data is bad.

## Write-safety classes — this determines MCP eligibility, not domain

Every entry carries one of three classes:

| Class | Count | Reachable from |
|---|---|---|
| `read` | 105 | CLI + MCP |
| `single-edit` (change one thing you already have permission to touch) | 51 | CLI + MCP |
| `create-or-bulk-or-destructive` | 46 | **CLI only** |

The 46 `create-or-bulk-or-destructive` entries are excluded from MCP entirely — creating
something new, or anything with real blast radius, isn't exposed as an agent tool call
at all in this product. If you need one of these and you're working through MCP tools,
tell the user it needs the CLI (or the console) instead of trying to route around it.

## 12 entries that throw an explicit "not yet implemented" error

These reference structural or derived request-body fields no CLI/MCP caller can supply
directly (e.g. a computed filter object the console UI builds client-side). Both the CLI
and MCP dispatch layers deliberately error rather than send an incomplete request:

`get_account_event_overview`, `get_account_activity`, `enrich_accounts`,
`get_user_activity`, `list_user_meetings`, `enrich_users`, `create_group`,
`create_segment`, `list_deals`, `create_deal`, `get_deal_activity`, `list_meetings`.

If you hit one of these, don't retry with different arguments — it's not a fixable
input problem, tell the user the operation isn't implemented yet.

## Other things worth knowing before you assume a capability exists

- **No `compare_*`-style tool exists anywhere in the 202 entries.** If asked to compare
  two things, do it yourself from two separate lookups — don't look for a comparison tool.
- **No entry returns a console URL** for the object it's about (dashboards, entity
  details — none of them). Don't fabricate one.
- **Pagination passes straight through to the real endpoint** — list-returning tools
  expose the underlying REST endpoint's own page/pageSize/cursor args. There's no
  CLI/MCP-imposed cap layered on top.
- **A 402 (insufficient credits) or 403 (tier-gated entitlement) error may not name the
  billing URL even though the product intends it to** — this is a known, confirmed,
  unfixed gap in the CLI/MCP error path (raw gateway string passthrough, no status-code
  branching). If you hit a billing-flavored error with no clear next step, tell the user
  to check `app.intempt.com/settings/billing` yourself rather than assuming the error
  message will point there.
- **`intempt use --org --project`** resolves the "Organization and project are required"
  error for a multi-org/multi-project account — mention this if a registry call fails
  with that specific message.
