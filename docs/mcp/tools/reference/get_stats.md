---
title: "get_stats"
domain: mcp-tools
category: reference
tldr: "get_stats(workspace) — index health, chunk counts, model status, cache hit rate, and graph connectivity (average edges per entity, directed density) for the current workspace; the observability tool, not the code-content one."
order: 7
---

<Callout variant="tldr">
`get_stats` answers "is the index fresh, how much is indexed, are
models loaded" — nested stats for index, models, cache, and graph in
one call.
</Callout>

## Parameters

- `workspace` (string, optional).

## Deep Index status

`status` is `"ready"` once Deep Index (embeddings) has completed for this
workspace and `"deep_index_running"` while it hasn't, including when the
last run was canceled or crashed before finishing. It used to read
`"ready"` unconditionally. The `deep_index` block carries the detail:
`ready` (boolean), `percent_complete` (number or null), and `note` (the
plain-language explanation, or null). Counts read while `deep_index_running`
are partial and still growing.

## When to use it (and when not to)

Call it for freshness/coverage/health questions about Contextual's own
state. Skip it if you need structural information about the codebase's
actual contents — use `search` or `graph_stats` instead.

## See also

- `cli/reference/general/stats` — the CLI equivalent.
- `mcp/tools/reference/get_doctor`, `mcp/tools/reference/graph_stats`.
- `mcp/tools/reference/get_telemetry` — usage/activity over time, not a snapshot.
