---
title: "graph_at_time"
domain: mcp-tools
category: reference
tldr: "graph_at_time(entity_id, timestamp, depth, limit, include_code, workspace) — a bitemporal snapshot of an entity and its graph neighborhood as they existed at a specific commit or ISO timestamp."
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
- `workspace` (string, optional).

## When to use it (and when not to)

Call it for explicit historical/audit questions about past state. Skip
it if you want current state — use `graph_traverse` or `get_file_
content` — or need a commit list — use `get_temporal_context`.

## See also

- `mcp/tools/reference/get_temporal_context`, `mcp/tools/reference/graph_traverse`.
