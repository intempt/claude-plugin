---
name: market
description: Intempt Market — check on or control a lifecycle/outreach journey, an A/B test or personalization experience, or look at raw event data. Use for "how's my campaign/flow doing," "is this test significant yet," "can I pause/stop this," or anything about triggering/tracking engagement.
---

# Intempt Market

Positioning frame: "Experiment the page. Orchestrate the journey. Attribute the
dollar." (Experimentation Lead + Lifecycle Marketer agents). This skill covers three
domains that are genuinely different jobs — read the right section, don't assume they're
interchangeable just because they're all "engagement."

## Outreach (journeys — 43 entries)

Multi-step automation: triggers → messages → control flow, sent to an audience. This is
console-built (a visual canvas, 13 node types) — **this skill is scoped to reading,
analyzing, and lifecycle-controlling an existing journey, not building the canvas
itself**, the same boundary Clay's own workflow-building skill draws for its product.

- **Lifecycle**: create → publish → pause → resume → stop. Most narrow lifecycle-action
  tools are consolidated — e.g. `set_journey_status` replaces 7 separate action tools —
  so reach for the consolidated tool rather than looking for one verb per state.
- **Agent nodes** (research/reply) inside a journey cost LLM credits to run — mention
  this if asked why a journey is consuming credits beyond simple sends.
- The 43 registry entries consolidate to far fewer MCP tools than that number implies —
  don't expect a 1:1 tool-per-entry count via MCP.

## Testing & personalization (experiences — 17 entries)

One `Experience` object, `Mode: experiment | personalization`. Real statistical
methodology behind this (CUPED variance reduction, mSPRT sequential testing, 6
confidence levels, primary/secondary metrics) — when reporting results, use the actual
returned significance/confidence values, don't eyeball "looks like it's winning."

**Critical gap to warn about:** server-side/flag-based experience delivery
(`Channel: server`) is present in the UI as a concept but **has no live creation path
anywhere in the product**. If a user asks to set up a server-side feature flag or
server-delivered experience, say clearly that this isn't buildable via any surface
today — don't attempt a registry call that would target a feature that doesn't exist.
There is also no Blu Chat creation/suggestion capability for experiences at all.

## Events (9 entries)

Two different things, only one of which this registry reaches:

- **Table** — named, filter-derived "Events" with per-event analytics. This is what the
  9 real registry entries cover.
- **Live** — an unfiltered, raw WebSocket event stream. Console-only; there's no
  CLI/MCP equivalent for a live feed, and trying to poll for one isn't the right model.

**Intent flag cross-feature gotcha**: an event's Intent flag aggregates into Users' and
Accounts' displayed "Intent level" (see `crm`'s note on that value's confusing polarity)
— but Events owns the *value*, while Users/Accounts own the *display*. If something
looks wrong about an intent level, the bug could be in either area; don't assume it's
this domain's fault just because the value originates here.

No Blu Chat integration exists for events, and there's no dedicated RBAC scope — errors
here surface as a generic 403, not a domain-specific permission message.
