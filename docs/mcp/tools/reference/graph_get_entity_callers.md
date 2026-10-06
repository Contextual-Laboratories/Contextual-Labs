---
title: "graph_get_entity_callers"
domain: mcp-tools
category: reference
tldr: "graph_get_entity_callers(entity_id, limit, include_code, no_preview, full_detail, gcf, workspace) — a flat, one-hop backward lookup: everything that directly calls or imports this entity."
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
  Besides a raw hash or an FQN, a bare symbol name (`LanceDBConnector`)
  or a repo-relative file path (`contextual/storage/connection.py`) also
  resolves. If several entities share a bare name, the most-referenced
  one is chosen; use an FQN when you need a specific one. See
  `troubleshooting/entity-not-found`.
- `limit` (integer, 1–50, default 20).
- `include_code` (boolean, default false).
- `no_preview` (boolean, default false) — omit the short code preview
  entirely instead of `include_code`'s default snippet.
- `full_detail` (boolean, default false) — an entity already returned at
  full detail earlier in this session collapses to a short
  `{id, name, already_shown: true}` reference by default (e.g. after a
  `graph_impact` call on an overlapping entity set); pass `true` to opt
  out for one call.
- `gcf` (boolean, default false).
- `workspace` (string, optional).

<Callout variant="note">
`potential_call` (speculative, confidence 0.35) edges are always
excluded here. To see speculative callers, use
`graph_traverse(predicate="potential_call")` or check
`graph_impact(change_type="rename")`, which surfaces them explicitly as
`speculative_callers`.
</Callout>

<Callout variant="note">
Every response carries `_meta.coverage` (`returned`, `has_more`,
`confidence`, and `capped_by` plus a matching `hint` when something was
held back). When `has_more` is true the response also leads with a
top-level `_status` sentence ("Showing 12 of 31 matches — …"). A result
of `confidence: "unknown"` (no callers found) is not the same as
"verified nothing calls this": the graph declines to guess at calls whose
receiver type it can't determine. `coverage.unresolved_count`, when
present, counts unresolved call sites that look like calls into this
entity (same name and language, from its file or a file that depends on
it): a heuristic of possible callers, not a confirmed count. See
`mcp/tools/explanation/reading-the-coverage-block`.
</Callout>

## When to use it (and when not to)

Call it for "who uses this function/module" with a known entity ID.
Skip it if you need multi-hop reverse traversal — use `graph_traverse`
with `direction="backward"` instead.

## See also

- `mcp/tools/reference/graph_traverse`, `mcp/tools/reference/graph_impact`.
- `mcp/tools/explanation/reading-the-coverage-block`.
