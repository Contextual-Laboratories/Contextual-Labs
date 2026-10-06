---
title: "graph_traverse"
domain: mcp-tools
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
  Besides a raw hash or an FQN, a bare symbol name (`LanceDBConnector`)
  or a repo-relative file path (`contextual/storage/connection.py`) also
  resolves. If several entities share a bare name, the most-referenced
  one is chosen; use an FQN when you need a specific one. See
  `troubleshooting/entity-not-found`.
- `depth` (integer, 1–3, default 1) — hops to walk.
- `predicate` (string, optional) — restrict to one edge type (e.g.
  `calls`, `imports`); pass `"potential_call"` to include speculative
  edges.
- `direction` (`"forward"` | `"backward"` | `"bi"`, default `"forward"`)
  — what this calls, what calls this, or both.
- `limit` (integer, optional) — max nodes to return; defaults scale with
  depth, hard-capped at 100.
- `include_code` (boolean, default false).
- `no_preview` (boolean, default false) — omit the short code preview
  entirely instead of `include_code`'s default snippet.
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

<Callout variant="note">
Every response carries `_meta.coverage` (`returned`, `has_more`,
`confidence`, and `capped_by` plus a matching `hint` when something was
held back). When `has_more` is true the response also leads with a
top-level `_status` sentence ("Showing 12 of 31 matches — …"). For
`direction="backward"` or `"bi"`, `coverage.dependents_confidence` judges
the backward results alone: `"resolved"` (dependents found), `"unknown"`
(none found — not the same as verified-absent), or `"speculative_only"`
when you passed `predicate="potential_call"`. See
`mcp/tools/explanation/reading-the-coverage-block`.
</Callout>

## When to use it (and when not to)

Call it once you have an `entity_id`/FQN from a search result and want
what it calls or what calls it. Skip it if you don't have an entity ID
yet (search first), or you only need direct callers —
`graph_get_entity_callers` is cheaper for that one case.

## See also

- `mcp/tools/reference/graph_get_entity_callers`,
  `mcp/tools/reference/graph_impact`.
- `mcp/tools/explanation/reading-the-coverage-block`.
