---
name: sell
description: Intempt Sell — draft/reply to prospect outreach, review an AI call summary, or work the sales motion. Use for "draft a follow-up to this lead," "summarize my last call with X," or anything about sequencing/closing. Uses crm for entity lookup.
---

# Intempt Sell

Positioning frame: "Find. Enrich. Sequence. Call. Close. Attribute." (SDR, Account
Executive, GTM Engineer agents). The "Find. Enrich." steps are `crm`'s job — use that
skill to look up the account/user/deal first. This skill picks up from **Sequence**
onward: outreach, calls, and (once shipped) scheduling.

## Outreach (blu-chat's outreach slice)

Real registry entries: `draft_outreach`, `reply_to_email`, `preflight_email`. These are
part of the same 33-entry `blu-chat` domain that `design`'s Brand Kit/personas/knowledge
base come from — the split isn't arbitrary: these three are the actual sales-sequencing
actions (matches "Sequence" in the positioning claim), the rest of blu-chat is brand
identity (matches "Design"). If you need brand voice to inform a draft, that's a `design`
lookup feeding into a `sell` action, not one domain doing both.

## Meetings (14 entries) — "Call"

An AI notetaker: a bot joins Google Meet/Zoom/Teams, records, transcribes
speaker-attributed, and summarizes via one of 9 backend-driven templates — General,
BANT, MEDDIC, Executive Brief, and others, each a real sales-qualification framework, not
a generic summary style. There's also a per-bullet "Expand by AI" capability on
summaries. When asked to summarize a call, pick the template that matches what's being
asked (a BANT-qualification question wants the BANT template's output, not General's).

Meeting *type*/booking configuration is a separate concern (see Scheduling below) — this
domain is the recording/transcript/summary layer only, after a meeting has already
happened or is happening.

## Scheduling — real, shipped product, no tool yet

Booking links (Individual/Round Robin/Collective meeting types, public booking pages)
are a complete, shipped Intempt product — not missing functionality. What's missing is
CLI/MCP tool coverage: zero registry entries exist for it today. If asked to "send a
booking link" or "schedule a call," tell the user this isn't reachable via any CLI/MCP
tool yet and point them to the console's Scheduling page — don't imply the feature
itself doesn't exist or is incomplete.
