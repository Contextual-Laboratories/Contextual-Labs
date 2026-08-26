---
title: Interpret a blast-radius report
domain: graph
category: how-to
tldr: "graph_impact ranks impacted entities by hop distance from the entity you're about to change, and — for a rename — adds a separately-labeled speculative_callers list. Read the ranking and the tiers before trusting a count."
order: 5
related:
  - graph/explanation/confidence-tiers-and-resolution.md
  - mcp/tools/reference/graph_impact.md
  - troubleshooting/entity-not-found.md
---

<Callout variant="tldr">
`graph_impact` answers "what breaks if I change this," ranked by how
directly each dependent is affected — not an unordered list. A rename
gets one extra, explicitly separate list of unconfirmed candidates you
should treat as leads, not facts.
</Callout>

## The shape of a response

```json
{
  "entity_id": "auth.py:AuthService.login",
  "change_type": "rename",
  "impacted_nodes": [
    {
      "id": "handlers.py:LoginHandler.post",
      "entity_type": "method",
      "impact_hop": 1,
      "via_predicate": "calls",
      "in_degree": 3,
      "out_degree": 7
    }
  ],
  "total_impacted": 1,
  "suggestion": "Changing `auth.py:AuthService.login` (rename) may affect 1 dependent entity(-ies).",
  "speculative_callers": [
    {"id": "legacy/old_auth.py:LegacyLogin.run", "entity_type": "function"}
  ],
  "_meta": {
    "returned": 1,
    "truncated": false,
    "total_retrieved": 1,
    "_resolution_summary": {"total_edges": 1, "by_tier": {"high": 1}}
  }
}
```

(Trimmed for readability — see `mcp/tools/reference/graph_impact` for
the complete field list.)

## Reading it in order

1. **`impacted_nodes` is already ranked**, primarily by `impact_hop` —
   direct dependents (hop 1) before indirect ones reached through
   another dependent (hop 2, 3, ...). A direct dependent breaking is a
   more urgent finding than an indirect one several hops away, and the
   ordering already reflects that; you don't need to re-sort by hand.
2. **`via_predicate` tells you the relationship type**, not just that a
   connection exists — an `inherits_from` dependent breaks differently
   than a `calls` dependent, and the predicate is how you tell them
   apart without re-deriving it from the entity's own code.
3. **`change_type` changes what counts as "impacted" at all** —
   `"delete"`, `"rename"`, and `"signature_change"` each use different
   edge types and traversal depth, since they break different things
   (a rename breaks callers by name; a signature change breaks callers
   regardless of name; a delete breaks anything that references the
   entity at all). Re-running with a different `change_type` on the
   same entity is expected to return a different result, not a bug.
4. **`speculative_callers` only appears for `change_type="rename"`**,
   and is deliberately kept separate from `impacted_nodes` rather than
   merged in — these are `potential_call` edges the resolver couldn't
   confirm statically. Treat every entry here as "check this by hand,"
   not as a confirmed dependent; see
   `graph/explanation/confidence-tiers-and-resolution` for why these
   exist at all instead of just being dropped.

## Before trusting a low or zero count

`"total_impacted": 0` only means "confirmed safe" if the entity itself
was actually found in the graph — a typo'd or non-existent `entity_id`
can also produce zero results, for a completely different reason. See
`troubleshooting/entity-not-found` for how to tell "genuinely zero
dependents" apart from "the graph doesn't know this entity exists" —
`graph_impact` surfaces that distinction explicitly rather than
returning the same empty-looking result for both.

Similarly, a low count with a large `speculative` share in
`_meta._resolution_summary` (visible whenever `speculative_callers` is
non-empty) means the graph is telling you it couldn't fully resolve
this entity's callers, not that the blast radius is actually small —
verify manually before treating it as safe.

## See also

- `graph/explanation/confidence-tiers-and-resolution` — what
  `speculative_callers` and the tier breakdown actually mean.
- `mcp/tools/reference/graph_impact` — full parameter reference.
- `graph/how-to/read-a-graph-traversal-result` — the equivalent read
  for `graph_traverse`/`graph_find_path`, ranked differently.
