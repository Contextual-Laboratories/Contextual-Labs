---
title: "get_doctor"
domain: mcp-tools
category: reference
tldr: "get_doctor(workspace) — the MCP-tool equivalent of contextual doctor: a full diagnostic across models, index, retrieval, graph, database, MCP daemon, and git, returning overall status plus per-subsystem checks with suggestions."
order: 6
---

<Callout variant="tldr">
`get_doctor` is what an AI client should call when something isn't
working — search returning nothing, tools erroring, models not loaded —
rather than guessing at the cause. Returns an overall status
(`ok`/`degraded`/`critical`) plus a per-subsystem breakdown with
concrete suggestions.
</Callout>

## Parameters

- `workspace` (string, optional) — diagnostics still run without one.

## When to use it (and when not to)

Call it when something is visibly broken, or the user asks "is
Contextual healthy?" Skip it if you only want index statistics — use
`get_stats` instead, it's cheaper and more specific.

## See also

- `cli/reference/general/doctor` — the CLI equivalent.
- `observability/how-to/interpreting-doctor-report`.
- `mcp/tools/reference/diagnose_issue` — correlates this with recent telemetry into ranked causes.
