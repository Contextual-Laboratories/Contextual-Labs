---
title: "graph_find_path"
domain: mcp-tools
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

Both endpoints also accept a bare symbol name or a repo-relative file
path. If several entities share a bare name, the most-referenced one is
chosen; use an FQN when you need a specific one.
- `max_depth` (integer, 1–5, default 5) — max hops to search before
  giving up.
- `workspace` (string, optional).

If no path exists within `max_depth`, the response says so explicitly
(`found: false`) rather than an empty, ambiguous result.

A path only follows real dependency and usage edges (calls, imports,
inheritance, instantiation and similar), the same family
of edges `graph_get_entity_callers` draws from. Containment edges such as "this class
defines this method" and provenance edges such as authorship are never
hops. Without that restriction, a class that was merely instantiated
somewhere near the source plus a "defines" edge to one of its methods
was enough to report a path to that method whether or not it is ever
called. A hop that carries edge metadata, such as `via: "raise"` for an
instantiation inside a raise statement, shows it under the hop's
`metadata` field. When the response is large, low-priority node fields
are dropped first (`_meta.omitted_fields`), and code detail is only
upgraded to full source while the shared token budget allows
(`_meta.response_budget`).

## When to use it (and when not to)

Call it for "is there a dependency between these two?" or tracing an
indirect relationship. Skip it if you want all dependents of one entity
(`graph_impact`) or direct one-hop neighbors (`graph_traverse`).

## See also

- `mcp/tools/reference/graph_traverse`, `mcp/tools/reference/graph_impact`.
