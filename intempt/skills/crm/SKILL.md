---
name: crm
description: Intempt CRM — look up and manage accounts, users/contacts, deals, and segments (saved lists). Use for "who is this account/user/deal," building a targeting list, or any entity lookup that feeds another product (Design, Marketing, Sales, Analytics all use this data).
---

# Intempt CRM

The entity/record layer underneath all four Intempt products — Design needs customer
data, Marketing needs an audience to target, Analytics needs entities to analyze, Sales
needs pipeline. This skill is that shared lookup/list-management layer: accounts (12
entries), users (13), deals (8), segments (10) — 43 real registry entries total. If your
actual task is a sales motion (drafting outreach, reviewing a call), use `sell`, which
references this skill for entity lookup rather than duplicating it.

## Looking someone up

Every `{id}`-parameterized users/accounts tool accepts a name or email directly — it
resolves to an internal ID via `POST /profile-lists/{users|accounts}`, first match, no
disambiguation on multiple matches. If a lookup returns someone unexpected, consider a
same-name collision before assuming bad data.

Consolidated detail views (`get_user_detail`, `get_account_detail`) replace what used to
be several narrow reads — pass the view you want rather than looking for a
dedicated tool per field.

## The Intent-level gotcha (users)

A user's Intent level can show **opposite polarity depending on which pane you're
reading** — a "bad sign" (red, High) on the Users List page and a "good sign" (green,
High) on that same user's Details sidebar. Same underlying value, two different visual
framings. If asked to interpret intent level, say what the number/level actually is and
let the human resolve the polarity, rather than asserting "this is good" or "this is
bad" yourself.

## Segments — and the naming trap around them

A **real segment** is a named, static-membership group of Users, created by bulk-select
on the Users list. It is never edited after creation — there's no add/remove-member
action, only create and reference. Segments feed Attributes, Experiences, and Journeys
as a targeting scope.

**`create_segment` cannot actually be called** — it's one of the registry's 12 known
structural-bodyKey gaps and throws an explicit "not yet implemented" error. If asked to
build a segment via CLI/MCP, tell the user this isn't dispatchable yet; segment creation
has to happen in the console today.

**"Segment" is overloaded three other ways in the product — don't conflate them:**

1. The **Users/Accounts list "Segment selector"** actually switches between **Lists** —
   a different entity from real segments.
2. The **Deals/Tasks/Meetings "Segment selector"** is hardcoded UI presets (All/Won/Lost,
   etc.) with **zero backend** — not wired to any real entity at all.
3. Only the thing described above (created via Users-list bulk-select, consumed by
   Attributes/Experiences/Journeys) is the real, registry-backed segment.

If a user says "segment," ask which they mean if it's ambiguous — the wrong assumption
here silently sends you down a UI path that isn't real.

## Known gaps — these throw "not implemented," not bad data

Several accounts/deals entries are among the registry's 12 structural-bodyKey gaps:
`enrich_accounts`, `get_account_event_overview`, `get_account_activity` (accounts);
`list_deals`, `create_deal`, `get_deal_activity` (deals); plus `create_group` (also
CRM-adjacent). If one of these errors with "not yet implemented," that's expected —
don't retry with different arguments.

**Deals has no AI assistance wired up in the product at all** ("Ask AI about the deal"
has no submit handler in the console) — don't imply deal analysis beyond what the
registry's real, structured reads (`get_deals_analytics` etc.) return.
