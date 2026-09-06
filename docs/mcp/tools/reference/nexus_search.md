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
If Deep Index hasn't finished yet for this workspace, the response's
stale warnings carry a `DYNAMIC_INDEX_ONLY` note flagging that semantic
results may still be incomplete — unlike `search`, which actively
reroutes to the keyword layer in that window, this is a heads-up rather
than a behavior change. See `indexing/explanation/how-indexing-works`.
</Callout>

## See also

- `mcp/tools/reference/search`, `mcp/tools/reference/graph_traverse`.
- `mcp/tools/explanation/tool-taxonomy`.
