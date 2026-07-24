---
title: "decision_search"
domain: mcp-tools
category: reference
tldr: "decision_search(query, limit, filters, workspace) — semantic search over ADRs by topic, returning ranked decisions with full context, the decision text, and consequences."
order: 21
---

<Callout variant="tldr">
`decision_search` answers "why was X chosen" or "is there an ADR about
Y" — semantic search over recorded architectural decisions, not a
status filter.
</Callout>

## Parameters

- `query` (string, required) — natural-language topic or question.
- `limit` (integer, 1–20, default 5).
- `filters` (object, optional) — reserved for forward compatibility;
  currently a no-op.
- `workspace` (string, optional).

## When to use it (and when not to)

Call it for architectural-reasoning questions about past design
choices. Skip it if you want to list all ADRs or filter by status — use
`decision_list` — or you're searching code behavior, not decisions —
use `search`.

## See also

- `mcp/tools/reference/decision_list`, `mcp/tools/reference/search`.
