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
  "_meta": { "returned": ..., "truncated": ..., "total_retrieved": ..., "include_code": ... },
  "nodes": [ ... ],
  "temporal": { "recent_commits": [...], "blame": {...}, "adrs": [...], "velocity_summary": {...} },
  "stats": { "total_nodes": ..., "stale_count": ..., "depth_used": ... },
  "latency_ms": ...,
  "_resolved_workspace": "..."
}
```

- `_meta.truncated` is `true` when the traversal found more nodes than
  `limit` kept after the diversity pass — check `_meta.hint` when this
  happens; it points you at `include_code=True` for a specific node or
  `get_file_content` for the full body, rather than raising `limit`
  blindly.
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
