---
title: Read a nexus_search result
domain: nexus
category: how-to
tldr: "Making sense of the enrichment fields nexus_search adds to each hit."
order: 1
related:
  - nexus/explanation/nexus-enrichment.md
  - nexus/explanation/nexus-signal-sources.md
  - graph/how-to/read-a-graph-traversal-result.md
  - mcp/tools/reference/nexus_search.md
---

<Callout variant="tldr">
A `nexus_search` response is `_meta`, a list of enriched `nodes`, a
`temporal` block covering the seed entities, and `stats` summarizing
the whole call — this page walks through what to read on each.
</Callout>

## Top-level shape

```
{
  "_status": "...",
  "_meta": { "returned": ..., "coverage": { "has_more": ..., "confidence": ... }, "include_code": ... },
  "nodes": [ ... ],
  "temporal": { "recent_commits": [...], "blame": {...}, "adrs": [...], "velocity_summary": {...} },
  "stats": { "total_nodes": ..., "stale_count": ..., "depth_used": ... },
  "latency_ms": ...,
  "_resolved_workspace": "..."
}
```

- `_status` is only present when `_meta.coverage.has_more` is `true` — a
  plain sentence ("Showing 12 of 31 matches — …") that puts the same
  completeness signal front and center instead of requiring you to
  check a field buried in `_meta` first.
- `_meta.coverage` says how much of the real answer you're looking at.
  `has_more: true` means something was held back; `capped_by` names
  what (`limit`, `hydration_cap`, `diversity_cap`, or `token_budget`)
  and `hint` says whether raising `limit` would help or whether you
  should narrow the query, drop `include_code`, or use `get_file_content`
  for a specific node's full body. `confidence` is `"resolved"`,
  `"resolved_but_capped"`, or `"unknown"` (nothing found). See
  `mcp/tools/explanation/reading-the-coverage-block`.
- `_meta.response_budget` and `_meta.omitted_fields` appear when the
  response was trimmed to fit its size budget: whole trailing nodes
  (counted in `excluded_by_budget`) or low-priority node fields.
- `stale_warnings` may carry `DYNAMIC_INDEX_ONLY` (this query's seeding
  fell back to keyword matching), `WEAK_ANN_SEED` or `WEAK_MATCH_SIGNAL`
  (the seed match was weak). See `mcp/tools/reference/nexus_search`.
- `temporal` is keyed on the semantic seed entities, not on every
  returned node — it's the same shape `get_temporal_context` returns.
  See `mcp/tools/reference/get_temporal_context` and
  `temporal/explanation/temporal-intelligence` for what each of its
  four fields means.
- `stats.depth_used` echoes back the (clamped 1–5) `depth` the call
  actually ran with. `stats.stale_count` is how many returned nodes
  have `is_stale: true` — a quick signal for whether this result set is
  worth double-checking before you rely on it.

## Reading one node

Each entry in `nodes` is one enriched node:

- `id`, `name`, `entity_type`, `scope` — identity. `scope` is the
  dotted path (file/class/function) this entity lives under.
- `start_line`/`end_line` plus `code_preview` (default), or `code_text`
  plus `context_block` when `include_code=True` — the code payload. A
  node whose body is too large for the response budget gets a
  `code_truncated: true` marker instead of silently shrinking it; use
  `get_file_content(file_path, start_line, end_line)` to read it in
  full.
- `author`, `commit_hash`, `valid_at`, `change_count`,
  `change_velocity` — who last touched it, when, and how often it
  tends to change.
- `staleness_score`, `is_stale` — the current staleness read described
  in `temporal/explanation/temporal-intelligence`, computed fresh at
  retrieval time rather than reused from indexing.
- `out_degree`, `in_degree` — how structurally central this node is
  (how many things it depends on vs. how many things depend on it).
- `edges` — the structural edges that connected this node into the
  result, each carrying a `confidence_tier` (`high`/`moderate`/`low`/
  `speculative`/`unknown` — see
  `graph/explanation/confidence-tiers-and-resolution`).
- `co_change_count` — always `0` on a `nexus_search` node. This tool's
  traversal doesn't follow `co_changes_with` edges at all (see
  `nexus/explanation/nexus-signal-sources`), so this field never
  populates here the way it can on `graph_traverse`. Use
  `co_change_analysis` if you need that signal.

<Callout variant="note">
Free-text fields sourced from your indexed code and history — `author`,
commit messages inside the `temporal` block, code bodies when
`include_code=True` — come back wrapped in
`<untrusted_content>...</untrusted_content>` markers. That's a
deliberate safety envelope telling the calling client to treat that
text as data, never as instructions, since it originates from your
repository's own content rather than from Contextual itself.
</Callout>

## If `nodes` comes back empty

`stats.note` explains why when the traversal couldn't find any seeds at
all — most commonly, graph node vectors haven't finished backfilling
yet on a very fresh index. Re-run `contextual index .` and retry once
it completes, or fall back to `search` in the meantime.

## See also

- `nexus/explanation/nexus-enrichment` — the pipeline that produces
  this shape.
- `graph/how-to/read-a-graph-traversal-result` — the equivalent guide
  for `graph_traverse`/`graph_find_path`, which share the same node
  serialization.
- `mcp/tools/reference/nexus_search`.
