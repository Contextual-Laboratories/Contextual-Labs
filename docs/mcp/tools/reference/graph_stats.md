---
title: "graph_stats"
domain: mcp-tools
category: reference
tldr: "graph_stats(entity_type, workspace) — aggregate graph numbers: entity count, edge count, density, entity-type breakdown, and predicate distribution."
order: 13
---

<Callout variant="tldr">
`graph_stats` answers "how many functions are indexed" or "how many
edges exist" — a coverage summary of the graph layer, not a lookup of
specific entities.
</Callout>

## Parameters

- `entity_type` (string, optional) — restrict counts to one type (e.g.
  `function`, `class`, `file`).
- `workspace` (string, optional).

## When to use it (and when not to)

Call it for coverage/summary questions about the graph. Skip it if you
need broad system health — use `get_stats` — or need to find specific
entities — use `search` or `graph_query`.

## See also

- `mcp/tools/reference/get_stats`, `mcp/tools/reference/graph_query`.
