---
title: Read a graph traversal result
domain: graph
category: how-to
tldr: "graph_traverse and graph_find_path return nodes ranked by hop distance, each carrying its own edges and confidence tiers — here's what each field means and what to check before trusting a result."
order: 4
related:
  - graph/explanation/confidence-tiers-and-resolution.md
  - mcp/tools/reference/graph_traverse.md
  - mcp/tools/reference/graph_find_path.md
---

<Callout variant="tldr">
A `graph_traverse` response is a ranked list of `nodes`, each carrying
its own edges back toward the entity you started from — not a flat,
unordered bag of everything reachable within `depth` hops.
</Callout>

## The shape of a response

```json
{
  "entity_id": "auth.py:AuthService.login",
  "resolved_id": "9f2a1c...",
  "direction": "backward",
  "depth": 2,
  "nodes": [
    {
      "id": "handlers.py:LoginHandler.post",
      "entity_type": "method",
      "scope": "handlers.py:LoginHandler.post",
      "lang": "python",
      "staleness_score": 0.12,
      "is_stale": false,
      "in_degree": 3,
      "out_degree": 7,
      "edges": [
        {"predicate": "calls", "confidence": 0.9, "confidence_tier": "high"}
      ]
    }
  ],
  "total_nodes": 1,
  "_meta": {
    "returned": 1,
    "truncated": false,
    "total_retrieved": 1,
    "_resolution_summary": {"total_edges": 1, "by_tier": {"high": 1}}
  }
}
```

(Trimmed to the fields that matter for reading a result — a real
response also carries `author`, `commit_hash`, `change_velocity`, and a
few other fields per node; see `mcp/tools/reference/graph_traverse` for
the complete parameter and field list.)

## What to check, in order

1. **`direction`** — confirms which question was actually answered.
   `"forward"` means "what this entity calls/uses"; `"backward"` means
   "what calls/uses this entity"; `"bi"` means both, merged into one
   list. Misreading this is the single easiest way to misread an
   otherwise-correct result.
2. **Each node's `edges` and their `confidence_tier`** — a node reached
   only through `low` or `speculative` edges is a weaker claim than one
   reached through `high` edges, even though both appear in the same
   `nodes` list. See
   `graph/explanation/confidence-tiers-and-resolution` for what each
   tier actually means.
3. **`_meta._resolution_summary`** — the fast way to gauge overall
   result quality without reading every node's edges individually: a
   `by_tier` breakdown dominated by `high`/`moderate` is a solid
   result; a large `speculative` share (only present at all if you
   passed `predicate="potential_call"` — it's excluded by default)
   means treat the result as leads, not confirmed facts.
4. **`_meta.truncated`** — if `true`, `nodes` doesn't contain
   everything that matched; `_meta.total_retrieved` has the real count
   and `_meta.hint` (when present) suggests a narrower follow-up query.

## `graph_find_path` reads differently

`graph_find_path` returns a single path (a list of edges connecting
`source_entity_id` to `target_entity_id`) rather than a ranked node
list, or an explicit "not connected within `max_depth` hops" result if
none exists. Absence of a path is itself informative — it means no
relationship the graph can see connects the two entities within the
depth you searched, not that the search failed.

## See also

- `graph/explanation/confidence-tiers-and-resolution` — what `high` /
  `moderate` / `low` / `speculative` actually mean.
- `mcp/tools/reference/graph_traverse`,
  `mcp/tools/reference/graph_find_path` — full parameter reference.
- `graph/how-to/interpret-a-blast-radius-report` — reading
  `graph_impact`'s result shape instead, which ranks by impact rather
  than by traversal hop alone.
