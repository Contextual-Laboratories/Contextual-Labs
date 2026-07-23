---
title: "graph_traverse"
domain: mcp-server
category: reference
tldr: "graph_traverse(entity_id, depth, predicate, direction, limit, include_code, gcf, workspace) — walks the code graph forward (what this calls), backward (what calls this), or both, from a known entity."
order: 8
---

<Callout variant="tldr">
`graph_traverse` walks structural dependencies outward or inward from a
known entity across multiple hops. `entity_id` accepts either a raw
entity hash or a human-readable FQN — no prior `search()` call needed
just to get the hash.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN
  (`path/to/file.py:ClassName.method`) to start from.
- `depth` (integer, 1–3, default 1) — hops to walk.
- `predicate` (string, optional) — restrict to one edge type (e.g.
  `calls`, `imports`); pass `"potential_call"` to include speculative
  edges.
- `direction` (`"forward"` | `"backward"` | `"bi"`, default `"forward"`)
  — what this calls, what calls this, or both.
- `limit` (integer, optional) — max nodes to return; defaults scale with
  depth, hard-capped at 100.
- `include_code` (boolean, default false).
- `gcf` (boolean, default false) — compact GCF-encoded text format.
- `workspace` (string, optional).

<Callout variant="note">
`potential_call` edges (confidence 0.35) are speculative — too ambiguous
to confirm at parse time — and are excluded by default. The response's
`_meta._resolution_summary` breaks down returned edges by confidence
tier; a high speculative count means many calls couldn't be statically
resolved and should be treated as advisory only, not a confirmed
dependency graph.
</Callout>

## When to use it (and when not to)

Call it once you have an `entity_id`/FQN from a search result and want
what it calls or what calls it. Skip it if you don't have an entity ID
yet (search first), or you only need direct callers —
`graph_get_entity_callers` is cheaper for that one case.

## See also

- `mcp-server/reference/graph_get_entity_callers`,
  `mcp-server/reference/graph_impact`.
