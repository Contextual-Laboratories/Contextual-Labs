---
title: Nexus signal sources
domain: nexus
category: explanation
tldr: "The three signals nexus_search blends — semantic proximity, structural graph traversal, and temporal recency — and why co-change coupling is deliberately not one of them."
order: 8
related:
  - nexus/explanation/nexus-enrichment.md
  - retrieval/explanation/retrieval-pipeline.md
  - graph/explanation/the-knowledge-graph.md
  - temporal/explanation/temporal-intelligence.md
  - mcp/tools/reference/co_change_analysis.md
---

<Callout variant="tldr">
`nexus_search` blends three signals into every node it returns: where
your query sits semantically, what's structurally connected to that
point in the graph, and how fresh each connected node's history is.
Co-change coupling is a real, separate signal in Contextual — it's just
not one of these three; `nexus_search`'s traversal skips it on purpose.
</Callout>

## Signal 1: semantic proximity

Your query is embedded with the same local model used everywhere else
in Contextual, then matched directly against the `node_vector` column
on the `entities` table — an approximate-nearest-neighbor lookup
straight against graph nodes, not against `search`'s chunk index. This
is what picks the seed nodes the rest of the call expands from. See
`retrieval/explanation/retrieval-pipeline` for how this differs from
the chunk-level embedding search `search` runs.

If node vectors haven't been populated yet — early in a fresh index,
before the backfill stage described in
`indexing/explanation/how-indexing-works` completes — this signal falls
back to a keyword match on entity name/scope instead of returning
nothing.

## Signal 2: structural graph proximity

From the semantic seeds, a breadth-first traversal follows real
structural edges outward — calls, imports, inherits, and the rest of
the taxonomy in `graph/explanation/the-knowledge-graph` — plus one
backward hop (who calls each seed) to cover the direction a purely
forward traversal misses. Each returned node carries the edges that
connected it into the result, with a `confidence_tier` per edge — see
`graph/explanation/confidence-tiers-and-resolution` for what `high`
through `speculative` mean.

## Signal 3: temporal recency

Every node the traversal picks up gets its current staleness score,
blame attribution, and recent-commit context attached before the
result is returned — computed at retrieval time, not looked up from a
stale cache. See `temporal/explanation/temporal-intelligence` for how
staleness is scored and where blame data comes from.

## The signal that's deliberately absent: co-change

`co_changes_with` edges — files or entities that tend to land in the
same commits — make up the large majority of all edges in the graph.
`nexus_search`'s traversal excludes them on purpose: following them
would both slow the BFS and dilute the structural context (callers,
callees, imports, inheritance) an AI client is actually asking for when
it reaches for this tool. That's why a `nexus_search` result's
`co_change_count` field is always zero — it isn't a weaker version of
the signal, it's simply not part of this traversal.

Co-change is a genuinely useful signal — it just lives in a dedicated
tool, `co_change_analysis`, built for exactly this: surfacing coupling
between entities that share commit history but have no structural
relationship at all (`is_undeclared_coupling`). Reach for it
separately when the question is "what else tends to change alongside
this," rather than expecting `nexus_search` to surface it as a
byproduct.

## See also

- `nexus/explanation/nexus-enrichment` — the full call sequence these
  three signals are assembled into.
- `nexus/how-to/read-a-nexus-search-result` — reading the fields each
  signal produces on a returned node.
- `mcp/tools/reference/co_change_analysis`.
