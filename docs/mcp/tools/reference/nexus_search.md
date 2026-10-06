---
title: "nexus_search"
domain: mcp-tools
category: reference
tldr: "nexus_search(query, depth, limit, include_code, gcf, workspace) — one call combining semantic search with graph traversal, returning enriched nodes with code, structural edges, authorship, and temporal context."
order: 2
---

<Callout variant="tldr">
`nexus_search` is the "give me everything around this" tool — semantic
search plus structural graph edges plus authorship plus temporal data,
in one round trip, with no prior `entity_id` required.
</Callout>

## Parameters

- `query` (string, required) — natural-language question or code/symbol
  name.
- `depth` (integer, 1–5, default 2) — BFS structural-edge hops from the
  seed nodes.
- `limit` (integer, 1–50, default 15) — max nodes after diversity
  selection.
- `include_code` (boolean, default false) — include full `code_text`
  per node instead of a short preview.
- `no_preview` (boolean, default false) — omit the short code preview
  entirely instead of `include_code`'s default ~70-line snippet; use it
  when you only need node identity/metadata and want to spend zero
  tokens on code text either way.
- `gcf` (boolean, default false) — return the compact GCF-encoded text
  format instead of JSON.
- `workspace` (string, optional).

## When to use it (and when not to)

Use it when you want the full structural neighborhood around a query
without manually chaining `search` + `graph_traverse` +
`get_temporal_context`, or when you already have an entity ID/FQN and
want the enriched bundle in one call. Skip it if you only need ranked
semantic snippets with no graph context (use `search`), or you're doing
co-change analysis (use `co_change_analysis`).

<Callout variant="note">
Every response carries `_meta.coverage` (`returned`, `has_more`,
`confidence`, and `capped_by` plus a matching `hint` when something was
held back, including when the response hit its size budget). When
`has_more` is true the response also leads with a top-level `_status`
sentence ("Showing 12 of 31 matches — …"). See
`mcp/tools/explanation/reading-the-coverage-block`.
</Callout>

<Callout variant="note">
If a query's seed selection had to fall back to keyword/lexical matching
instead of an embedding match — typically because Deep Index hasn't
finished embedding what the query needed — the response's
`stale_warnings` carry a `DYNAMIC_INDEX_ONLY` entry. The warning is tied
to that specific call, not to the workspace as a whole: a query answered
from embeddings that already exist doesn't get it, even while Deep Index
is still running. It's a heads-up rather than a behavior change. See
`indexing/explanation/how-indexing-works`.
</Callout>

<Callout variant="note">
`WEAK_ANN_SEED` appears in `stale_warnings` when the best embedding match
scored below the real-match floor, so the search fell back to keyword
seeding instead of expanding the graph from a seed already known to be
unrelated. `WEAK_MATCH_SIGNAL` appears when the top matches cleared that
floor but didn't stand out from the rest of the pool. Either one means
the structural neighborhood may be anchored to the wrong place; try
different search terms.
</Callout>

## See also

- `mcp/tools/reference/search`, `mcp/tools/reference/graph_traverse`.
- `mcp/tools/explanation/reading-the-coverage-block`.
- `mcp/tools/explanation/tool-taxonomy`.
