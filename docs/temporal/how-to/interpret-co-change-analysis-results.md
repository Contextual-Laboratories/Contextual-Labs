---
title: Interpret co-change analysis results
domain: temporal
category: how-to
tldr: "Reading co_change_analysis output for refactor planning."
order: 5
related:
  - mcp/tools/reference/co_change_analysis.md
  - nexus/explanation/nexus-signal-sources.md
  - graph/explanation/the-knowledge-graph.md
---

<Callout variant="tldr">
`co_change_analysis` ranks partners by `coupling_strength`, a
query-invariant fraction — not a raw commit count — and flags each one
`is_undeclared_coupling: true` when nothing structural connects it to
your focal entity. The undeclared ones are the actual signal worth
acting on.
</Callout>

## The shape of a result

```
{
  "entity": { "entity_id": ..., "name": ..., "entity_type": ..., "scope": ... },
  "co_change_partners": [
    {
      "entity_id": ..., "name": ..., "entity_type": ..., "scope": ...,
      "co_change_count": 14,
      "coupling_strength": 0.42,
      "has_structural_dependency": false,
      "is_undeclared_coupling": true
    },
    ...
  ],
  "summary": {
    "total_partners": ...,
    "undeclared_couplings": ...,
    "strongest_coupling": { "entity_id": ..., "name": ..., "coupling_strength": ... }
  }
}
```

## Reading `coupling_strength`

`coupling_strength` is a Jaccard-style ratio, not `co_change_count`
itself:

```
coupling_strength = co_change_count / (focal_changes + partner_changes − co_change_count)
```

— the fraction of either entity's total changes that landed in a
commit shared with the other. This is deliberately normalized against
each entity's own total change history rather than against the
strongest partner in this one result set: normalizing against the
local max would make coupling_strength hit `1.0` for every partner
whenever they all happen to tie on raw count, even when none of them
are actually dominant. A `coupling_strength` close to `1.0` means these
two entities essentially always change together; a low one means they
've coincidentally shared a few commits without a real pattern.

`co_change_count` is the real historical commit count, recovered from
each triple's metadata rather than its stored `weight` — `weight`
itself is recency-decayed for ranking purposes elsewhere in the graph,
so an old-but-genuinely-well-supported pairing would otherwise read as
indistinguishable from one-commit noise.

## `has_structural_dependency` and `is_undeclared_coupling`

Every partner is checked against the graph for a direct `calls`,
`imports`, `inherits_from`, or `implements` edge to the focal entity.

- `has_structural_dependency: true` — the coupling is at least partly
  explained by code you can already see (an import, a call). Expected,
  lower-priority signal.
- `is_undeclared_coupling: true` (equivalently, no structural
  dependency) — these two entities change together in git history with
  **nothing in the code connecting them**. This is the actually useful
  finding: a hidden dependency — shared assumptions, a config file and
  the code that reads it, a test fixture and what it exercises — that
  static analysis alone would never surface.

`summary.undeclared_couplings` gives you the count up front; check that
before scanning the full partner list.

## Before a refactor

Read the partner list ranked by `coupling_strength`, focusing on the
undeclared ones first — those are the files most likely to need a
matching change that nothing in the dependency graph would warn you
about. `summary.strongest_coupling` is a shortcut to the single
strongest signal without scanning the whole list.

A partner with a low `co_change_count` (below the configured
`co_change_min_commits` floor, `2` by default) never appears at all —
this filters out one-off incidental co-edits (a mass reformat, an
unrelated commit that happened to touch two files) from ever masquerading
as a real coupling signal.

## See also

- `mcp/tools/reference/co_change_analysis` — parameters and when to
  reach for this tool over `graph_impact`.
- `nexus/explanation/nexus-signal-sources` — why `nexus_search` itself
  doesn't surface this signal, and reaches for this tool instead.
