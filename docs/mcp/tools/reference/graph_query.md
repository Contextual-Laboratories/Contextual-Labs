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

<Callout variant="note">
Each returned entity carries `in_degree` and `out_degree`. They count
every edge attached to the entity, including co-change and authorship
edges, which make up the large majority of edges in most workspaces. They
are therefore not a count of what depends on the entity. For "what breaks
if I change this," use `graph_impact`, which follows only real dependency
edges, so the two numbers can legitimately differ. The values returned are
counted live. A degree condition in the `WHERE` clause, such as
`in_degree > 3`, filters on a snapshot refreshed at index time and every few
hours, so it can lag edits made since. When a result set is
large, fields drop under a response budget with `edges` kept longest, and
the dropped field names are listed in a top-level `omitted_fields`.
</Callout>

<Callout variant="warning">
Write keywords (`INSERT`, `UPDATE`, `DELETE`, etc.) are rejected before
the query ever touches the database — this tool is read-only by
construction, not just by convention. The `query` fragment must also
parse as a single plain boolean predicate: `UNION`, `INTERSECT`,
`EXCEPT`, and any other multi-statement or compound-query construct are
rejected outright, not just filtered down to their first clause.
</Callout>

## When to use it (and when not to)

Call it for structured, property-based bulk lookup. Skip it for
semantic-meaning search (`search`) or multi-hop traversal
(`graph_traverse`).

## See also

- `mcp/tools/reference/search`, `mcp/tools/reference/graph_stats`.
