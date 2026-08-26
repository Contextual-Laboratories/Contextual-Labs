---
title: The audit log
domain: observability
category: explanation
tldr: "The SOC2-style tool-call audit trail, its 90-day retention, and how it differs from ephemeral telemetry."
order: 6
related:
  - observability/explanation/how-tracing-and-logging-work.md
  - observability/reference/logs-and-retention-reference.md
  - cli/reference/general/activity.md
  - mcp/tools/reference/get_telemetry.md
---

<Callout variant="tldr">
Every MCP tool call is recorded in `audit_log`: which tool, which
workspace, how long it took, and whether it succeeded — always, not as
an opt-in tracing feature, and retained for 90 days rather than the
7-day window ephemeral telemetry gets. It stores hashes of the actual
arguments and result, not the raw content itself.
</Callout>

## What gets recorded, and what doesn't

Every request through the MCP daemon writes one row: `tool_name`, the
resolved `workspace_id`, `latency_ms`, `outcome` (`success`/`error`/
`timeout`), a `trace_id`/`span_id` when tracing produced one, and
`created_at`. Rather than storing the actual arguments or the actual
result, it stores a BLAKE3 hash of each (`args_hash`, `result_hash`) —
enough to prove a given call happened with given inputs/outputs (or
diff two calls against each other) without keeping a second copy of
your code or query text sitting in a separate table. Protocol-level
JSON-RPC bookkeeping (`initialize`, `tools/list`) isn't a real tool
call and is filtered out wherever this data is read back.

## Always on, independent of tracing config

Unlike `otel_spans`/`otel_logs` (gated by `export_to_lancedb`, see
`observability/explanation/how-tracing-and-logging-work`), audit
logging isn't a tracing feature you can turn off — it's a fixed
middleware that runs on every request, the same way a SOC2-style audit
trail is expected to: independent of whatever debugging/tracing
configuration is active. Writes are non-blocking (fire-and-forget from
the daemon's perspective) so a slow or failed audit write can never add
latency to, or break, the tool call it's describing.

## A longer, separate retention window

`audit_log` is pruned on its own 90-day window
(`audit_log_retention_days`), deliberately longer and independent from
the 7-day window ephemeral telemetry gets — an audit trail and
throwaway performance data don't belong to the same retention policy
just because one background loop happens to sweep both. See
`observability/reference/logs-and-retention-reference` for the exact
mechanics.

## Where this data actually surfaces

Nothing reads `audit_log` directly — `contextual activity` (CLI) and
`get_telemetry`/`diagnose_issue` (MCP tools) are the read paths, each
aggregating it into call counts, error rates, latency percentiles, and
per-tool breakdowns rather than exposing raw rows.

## See also

- `cli/reference/general/activity` — the CLI view onto this same table.
- `mcp/tools/reference/get_telemetry`, `mcp/tools/reference/diagnose_issue`.
- `observability/reference/logs-and-retention-reference`.
