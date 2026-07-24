---
title: "decision_create"
domain: mcp-tools
category: reference
tldr: "decision_create(title, context, decision, consequences, authored_by, workspace) — creates a new Architecture Decision Record in proposed state, embedded automatically for later semantic search."
order: 17
---

<Callout variant="tldr">
`decision_create` records a new architectural decision as a proposed
ADR — title, context, the decision itself, and its consequences — and
embeds it so `decision_search` can find it later.
</Callout>

## Parameters

- `title` (string, required) — short ADR title.
- `context` (string, required) — background/problem statement.
- `decision` (string, required) — what was chosen.
- `consequences` (string, required) — tradeoffs and follow-on effects.
- `authored_by` (string, optional) — author name/identifier.
- `workspace` (string, optional).

Returns the new `adr_id` in `proposed` status — use `decision_accept`
to make it the active decision.

## When to use it (and when not to)

Call it when a decision is being made or documented for the first time.
Skip it if the decision might already exist — check `decision_list`
first to avoid duplicates.

## See also

- `mcp/tools/reference/decision_accept`, `mcp/tools/reference/decision_list`.
- `mcp/tools/how-to/record-and-track-an-architectural-decision`.
