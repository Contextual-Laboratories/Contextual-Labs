---
title: Retrieval configuration reference
domain: retrieval
category: reference
tldr: "Every [retrieval] field in config.toml, its default, and what it actually changes — set via contextual config, in the current workspace."
related:
  - retrieval/explanation/hybrid-ranking-and-mmr-diversity.md
  - cli/reference/config/config.md
---

<Callout variant="tldr">
Every field below lives under `[retrieval]` in a workspace's
`config.toml` (open it with `contextual config`). Defaults shown are
what ships when a field is omitted — an empty or partial `[retrieval]`
section falls back to these per-field, not as an all-or-nothing block.
</Callout>

## Candidate pool sizes

| Field | Default | What it changes |
|---|---|---|
| `bm25_candidates` | `100` | How many top BM25 results are pulled before fusion. |
| `dense_candidates` | `100` | How many top dense (vector) results are pulled before fusion. |
| `rrf_candidates` | `50` | How many fused candidates survive RRF fusion and move on to the MMR diversity pass. |

Raising `bm25_candidates`/`dense_candidates` widens the pool each
method contributes to fusion — more recall potential per method, more
fusion work. `rrf_candidates` is the narrower funnel after fusion,
before diversity selection; it doesn't need to match the wider input
pools.

## Fusion (Reciprocal Rank Fusion)

| Field | Default | What it changes |
|---|---|---|
| `rrf_k` | `60` | Rank-damping constant — higher flattens the gap between a method's top and lower-ranked hits; lower sharpens it. |
| `rrf_dense_weight` | `0.50` | Dense (embedding) search's weight in fusion. |
| `rrf_bm25_weight` | `0.35` | BM25 keyword search's weight in fusion. |
| `rrf_trigram_weight` | `0.15` | Trigram identifier search's weight in fusion. |

These three weights don't have to sum to any particular value — they're
independent per-method multipliers, not a normalized split. See
`retrieval/explanation/hybrid-ranking-and-mmr-diversity` for the exact
fusion formula these feed into.

## MMR diversity

| Field | Default | What it changes |
|---|---|---|
| `mmr_enabled` | `true` | Whether the MMR diversity pass runs at all after fusion. |
| `mmr_lambda` | `0.7` | Relevance/diversity tradeoff — closer to `1.0` favors relevance, closer to `0.0` favors diversity. |
| `mmr_max_chunks_per_file` | `2` | Hard cap on chunks from the same file in one `search` result set. |
| `mmr_max_chunks_per_symbol` | `1` | Hard cap on chunks from the same symbol (function/class/etc.) in one result set. |
| `mmr_max_graph_nodes_per_file` | `2` | The same per-file cap, applied separately to graph-node diversity selection (`nexus_search`, `graph_traverse`, `graph_impact`) rather than chunk-level `search` results. |

Setting `mmr_enabled = false` skips diversity selection entirely — the
fused, ranked list is returned as-is, which can mean several
near-duplicate or same-file chunks filling most of a result set.

## Hybrid mode and fallback

| Field | Default | What it changes |
|---|---|---|
| `hybrid_enabled` | `true` | Whether dense and trigram search run at all, alongside BM25. |
| `fallback_bm25_only` | `true` | If hybrid retrieval is unavailable (e.g. the vector index isn't ready), fall back to BM25-only instead of failing the query outright. |

## Vector search and caching

| Field | Default | What it changes |
|---|---|---|
| `ef` | `0` | Query-time candidate-list size for the vector index's HNSW search. `0` means "let LanceDB use its own default" rather than a fixed override. |
| `query_cache_ttl_seconds` | `300` | How long a repeated identical query can be served from cache before it's re-run. |
| `ann_index_row_threshold` | `50000` | Row count at which the incremental indexer starts building a background approximate-nearest-neighbor index for faster vector search on large workspaces. Below this, an exact flat scan is fast enough on its own. Set to `0` to disable ANN-index maintenance entirely. |

<Callout variant="note">
`ann_index_row_threshold` affects indexing-time behavior (whether an
ANN index gets built at all), not query-time — it's grouped here
because it's still a `[retrieval]` field, not because it changes what
happens during a `search` call itself.
</Callout>

## See also

- `retrieval/explanation/hybrid-ranking-and-mmr-diversity` — the exact
  formulas these fields feed into.
- `retrieval/how-to/tune-retrieval-quality-for-your-codebase` —
  practical guidance on which fields to actually touch.
- `cli/reference/config/config` — opening and editing `config.toml`.
