---
title: Temporal query reference
domain: temporal
category: reference
tldr: "How get_temporal_context and graph_at_time map their inputs onto real git history."
order: 6
related:
  - mcp/tools/reference/get_temporal_context.md
  - mcp/tools/reference/graph_at_time.md
  - temporal/explanation/temporal-intelligence.md
  - temporal/explanation/staleness-scoring-and-bitemporal-fields.md
---

<Callout variant="tldr">
`get_temporal_context` answers "what's this entity's history" over a
lookback window; `graph_at_time` answers "what did the graph look like
at this exact point" as a reconstructed snapshot. Both resolve
`entity_id` the same way; they diverge in what "when" means to each.
</Callout>

## Entity resolution (shared by both tools)

`entity_id` accepts either a 64-char lowercase-hex BLAKE3 hash or an
FQN string (`path/to/file.py:ClassName.method`). An FQN is hashed to
its ID via the same deterministic function used everywhere else in the
graph. If that lookup doesn't resolve, both tools retry it as a bare
file entity (`{rel_path}:__file__`) before giving up — file entities
are keyed by that convention, not the bare path, so a plain
`"src/foo.py"` input needs this second attempt to resolve at all.

## `get_temporal_context`: a lookback window over one entity

- `window_days` (1–365, default 30) — how far back `recent_commits`
  looks. Only `entity_commits` rows within this window are considered;
  it does not affect `blame` or `adrs`.
- `recent_commits` comes from a two-step join: `entity_commits` maps
  this entity to commit hashes, then `commits` supplies each hash's
  author/message/timestamp. The `commits` table can carry duplicate
  rows for the same hash (repeated indexing runs each upserting before
  seeing each other's write); the query overfetches and dedupes by
  hash before capping at 5, so "5 recent commits" reliably means 5
  distinct commits, not the same one repeated.
- `blame` is read directly from the `entities` table's own `author`/
  `commit_hash` columns — populated once during indexing's blame
  enrichment pass — not from the separate blame cache (which is keyed
  by file path + content hash, not entity ID, and would return nothing
  here).
- `blame_confidence.degraded: true` (with `reasons: ["shallow_clone"]`)
  signals that the workspace is a shallow git clone: blame and commit
  history can't see past the clone's truncation boundary, so lines
  whose true origin lies before it get attributed to a synthetic graft
  commit instead of their real author — a real degradation, not a bug,
  worth checking before trusting attribution on an old line in a
  shallow checkout.
- `adrs` — ADRs linked to this entity via a `motivated_by` edge,
  restricted to `accepted` status, capped at 3.
- `velocity_summary` — `commit_count` and `most_active_author` over
  the same `recent_commits` set, not a separate query.

## `graph_at_time`: a bitemporal snapshot at one instant

- `timestamp` accepts either an ISO 8601 timestamp or a git commit SHA
  (7–40 hex characters). A full 40-char SHA is matched exactly; a
  shorter prefix is matched against every commit sharing it and
  resolved to the most recent match — the git-like expectation when a
  short SHA is ambiguous.
- The resolved timestamp becomes an AS-OF filter against every
  entity's and edge's valid-time range (`valid_at`/`invalid_at` — see
  `temporal/explanation/staleness-scoring-and-bitemporal-fields`): a
  row counts as present in the snapshot only if its valid-time range
  actually covers that instant.
- The neighborhood traversal is restricted to structural predicates
  only — `calls`, `calls_polymorphic`, `imports`, `instantiates`,
  `inherits_from`, `implements`, `defines`, `references` — at a
  confidence floor of `0.55`. `co_changes_with` and `authored_by` are
  excluded outright: a historical snapshot is answering "what was the
  code structure," and a correlation or provenance edge doesn't
  reconstruct that.
- Each traversal hop is capped at 500 edges; a hop that would exceed
  this sets `frontier_truncated: true` in the response, meaning the
  returned neighborhood may be missing nodes beyond that hop even
  though the snapshot itself resolved correctly.

## See also

- `mcp/tools/reference/get_temporal_context`, `mcp/tools/reference/graph_at_time` — full parameter lists.
- `temporal/explanation/temporal-intelligence` — the blame pipeline and bitemporal model both tools read from.
