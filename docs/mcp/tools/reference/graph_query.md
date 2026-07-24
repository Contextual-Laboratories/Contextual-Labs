---
title: "graph_query"
domain: mcp-tools
category: reference
tldr: "graph_query(query, limit, gcf, workspace) — a read-only WHERE-clause filter over the entity graph for bulk lookup by property (e.g. all functions by one author), not by semantic meaning. Write keywords are rejected."
order: 14
---

<Callout variant="tldr">
`graph_query` runs a read-only SQL WHERE-clause fragment against the
entity table for bulk, property-based lookup — "all Python functions,"
"everything authored by X" — not semantic search.
</Callout>

## Parameters

- `query` (string, required) — read-only WHERE-clause fragment over
  entity columns, e.g. `"entity_type = 'function' AND author = 'x'"`.
- `limit` (integer, 1–200, default 50).
- `gcf` (boolean, default false).
- `workspace` (string, optional).

<Callout variant="warning">
Write keywords (`INSERT`, `UPDATE`, `DELETE`, etc.) are rejected before
the query ever touches the database — this tool is read-only by
construction, not just by convention.
</Callout>

## When to use it (and when not to)

Call it for structured, property-based bulk lookup. Skip it for
semantic-meaning search (`search`) or multi-hop traversal
(`graph_traverse`).

## See also

- `mcp/tools/reference/search`, `mcp/tools/reference/graph_stats`.
