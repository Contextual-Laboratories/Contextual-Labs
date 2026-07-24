---
title: "get_stats"
domain: mcp-tools
category: reference
tldr: "get_stats(workspace) — index health, chunk counts, model status, cache hit rate, and graph density for the current workspace; the observability tool, not the code-content one."
order: 7
---

<Callout variant="tldr">
`get_stats` answers "is the index fresh, how much is indexed, are
models loaded" — nested stats for index, models, cache, and graph in
one call.
</Callout>

## Parameters

- `workspace` (string, optional).

## When to use it (and when not to)

Call it for freshness/coverage/health questions about Contextual's own
state. Skip it if you need structural information about the codebase's
actual contents — use `search` or `graph_stats` instead.

## See also

- `cli/reference/general/stats` — the CLI equivalent.
- `mcp/tools/reference/get_doctor`, `mcp/tools/reference/graph_stats`.
