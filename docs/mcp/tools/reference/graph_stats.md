---
title: "graph_stats"
domain: mcp-tools
category: reference
tldr: "graph_stats(entity_type, workspace) — aggregate graph numbers: entity count, edge count, average edges per entity, directed density, entity-type breakdown, and predicate distribution."
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

<Callout variant="note">
The response reports `average_edges_per_entity` and `directed_density`
separately — not a single `density` field. `average_edges_per_entity`
(edge count / entity count) is the more intuitive "how connected is this
graph" number for a typical codebase and is usually well above 1;
`directed_density` (edges / all possible directed edges) is the real
graph-theoretic density and is typically a very small fraction even for a
richly-connected graph. Treating the old combined `density` field as a
0-1 fraction was itself the bug this replaced — check which one you
actually want before comparing across workspaces.
</Callout>

## When to use it (and when not to)

Call it for coverage/summary questions about the graph. Skip it if you
need broad system health — use `get_stats` — or need to find specific
entities — use `search` or `graph_query`.

## See also

- `mcp/tools/reference/get_stats`, `mcp/tools/reference/graph_query`.
