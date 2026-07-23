---
title: "graph_find_path"
domain: mcp-server
category: reference
tldr: "graph_find_path(source_entity_id, target_entity_id, max_depth, workspace) — finds the shortest dependency path between two entities, hop by hop, with predicate and confidence at each step."
order: 11
---

<Callout variant="tldr">
`graph_find_path` traces the shortest indirect dependency chain between
two entities — "how does X connect to Y?" — returning each hop's
predicate and confidence.
</Callout>

## Parameters

- `source_entity_id` (string, required) — hash or FQN of the starting
  entity.
- `target_entity_id` (string, required) — hash or FQN of the entity to
  find a path to.
- `max_depth` (integer, 1–5, default 5) — max hops to search before
  giving up.
- `workspace` (string, optional).

If no path exists within `max_depth`, the response says so explicitly
(`found: false`) rather than an empty, ambiguous result.

## When to use it (and when not to)

Call it for "is there a dependency between these two?" or tracing an
indirect relationship. Skip it if you want all dependents of one entity
(`graph_impact`) or direct one-hop neighbors (`graph_traverse`).

## See also

- `mcp-server/reference/graph_traverse`, `mcp-server/reference/graph_impact`.
