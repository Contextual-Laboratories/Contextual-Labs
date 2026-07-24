---
title: "graph_get_entity_callers"
domain: mcp-tools
category: reference
tldr: "graph_get_entity_callers(entity_id, limit, include_code, gcf, workspace) — a flat, one-hop backward lookup: everything that directly calls or imports this entity."
order: 9
---

<Callout variant="tldr">
`graph_get_entity_callers` is the cheap, direct answer to "who uses
this function?" — a flat one-hop backward lookup, not a multi-hop
traversal.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to find callers
  of.
- `limit` (integer, 1–50, default 20).
- `include_code` (boolean, default false).
- `gcf` (boolean, default false).
- `workspace` (string, optional).

<Callout variant="note">
`potential_call` (speculative, confidence 0.35) edges are always
excluded here. To see speculative callers, use
`graph_traverse(predicate="potential_call")` or check
`graph_impact(change_type="rename")`, which surfaces them explicitly as
`speculative_callers`.
</Callout>

## When to use it (and when not to)

Call it for "who uses this function/module" with a known entity ID.
Skip it if you need multi-hop reverse traversal — use `graph_traverse`
with `direction="backward"` instead.

## See also

- `mcp/tools/reference/graph_traverse`, `mcp/tools/reference/graph_impact`.
