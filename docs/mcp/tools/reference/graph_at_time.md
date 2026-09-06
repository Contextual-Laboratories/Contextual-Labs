---
title: "graph_at_time"
domain: mcp-tools
category: reference
tldr: "graph_at_time(entity_id, timestamp, depth, limit, include_code, no_preview, workspace) — a bitemporal snapshot of an entity and its graph neighborhood as they existed at a specific commit or ISO timestamp, snapping a raw timestamp to the nearest indexed commit."
order: 15
---

<Callout variant="tldr">
`graph_at_time` answers "what did this look like as of commit X" or
"how was the graph structured on this date" — a real historical
snapshot, not current state.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to snapshot.
- `timestamp` (string, required) — ISO 8601 timestamp or git commit SHA
  (7–40 hex chars).
- `depth` (integer, 1–3, default 1) — neighborhood hops to include.
- `limit` (integer, 1–40, default 20).
- `include_code` (boolean, default false).
- `no_preview` (boolean, default false) — omit the short code preview
  entirely instead of `include_code`'s default snippet.
- `workspace` (string, optional).

<Callout variant="note">
A raw ISO timestamp doesn't need to match a commit exactly — it snaps to
the nearest indexed commit, and the response's `status` field tells you
how that resolved: `"found"` (a real snapshot), `"history_out_of_range"`
(the timestamp is outside this entity's known commit range),
`"timestamp_unresolved"` (couldn't snap to anything), or
`"entity_not_present"` (the entity itself didn't exist at that point).
`resolution_distance_seconds` reports how far the snapped commit actually
is from the timestamp you asked for — a large value means the nearest
match may not represent what you actually meant. The response also
carries `snapshot_relative_time` ("3 days ago" alongside the exact
`snapshot_at` timestamp) and `_meta.temporal_confidence` — the same
`{degraded, reasons}` shape used across every history-derived tool (see
`get_temporal_context`).
</Callout>

## When to use it (and when not to)

Call it for explicit historical/audit questions about past state. Skip
it if you want current state — use `graph_traverse` or `get_file_
content` — or need a commit list — use `get_temporal_context`.

## See also

- `mcp/tools/reference/get_temporal_context`, `mcp/tools/reference/graph_traverse`.
