---
title: "get_telemetry"
domain: mcp-tools
category: reference
tldr: "get_telemetry(since, tool_name, errors_only, workspace) — recent tool-call volume, error counts, and latency percentiles from the audit trail, plus slow operations and recent errors from OpenTelemetry tracing."
order: 23
---

<Callout variant="tldr">
`get_telemetry` answers "what's actually been happening" over a time
window — call volume, errors, and latency — as evidence to gather
before diagnosing a problem, not a live health check.
</Callout>

## Parameters

- `since` (string, default `"24h"`) — time window, a number plus `h` or `d`.
- `tool_name` (string, optional) — restrict to one tool.
- `errors_only` (boolean, default false) — only failed calls / error spans.
- `workspace` (string, optional).

Returns `activity` (`total_calls`, `raised_errors`, `avg`/`p95`
`latency_ms`, `by_tool`, `total_tokens`, `avg_tokens_per_call`,
`priciest_tools`), `recent_errors`, `internal_error_span_count`,
`slow_operations`, `priciest_operations`, and `process_metrics`.

`process_metrics` is the running daemon's operational metrics — embed queue
wait and compute histograms, chunks embedded, background work deferred while a
bulk index ran, git subprocess latency, and whether a bulk job currently holds
the machine-wide model lease. Unlike everything else here it is **not** a window
over `since`: it is in-memory and covers the daemon's life since its last
restart. Internal daemon calls (such as an indexing job embedding through the
daemon) appear as their own `internal.embed` / `internal.index` spans and are
not counted as tool calls or tokens.

## Two distinct, non-interchangeable error counts

`activity.raised_errors` counts calls that actually raised an exception
(`audit_log` outcome ≠ `success`). `internal_error_span_count` counts
operations that hit an internal error path but still returned a
structured response (e.g. a validation failure) — these show
`outcome='success'` in the audit trail even though something went
wrong. Seeing `raised_errors=0` next to a non-empty `recent_errors`
list is expected, not a contradiction — check both.

`trace_data_available: false` means OpenTelemetry export hasn't
recorded anything for this window; `activity` (sourced from the audit
trail, a separate mechanism) is unaffected either way. See
`observability/explanation/how-tracing-and-logging-work` for why these
are two different data sources with different availability.

`_meta.telemetry_sources` labels this explicitly in the response itself:
`activity`'s `avg_latency_ms`/`p95_latency_ms` come from `audit_log` —
completed MCP tool-call round-trips only (successful and failed alike,
unless `errors_only` narrowed it), excluding protocol/session bookkeeping
calls. `slow_operations` comes from `otel_spans` — every instrumented
span in the window, not just tool calls, including internal indexing/
pipeline operations that can run far longer than any tool-call
round-trip (narrow it to one tool's own spans by passing `tool_name`). A
small `activity.p95_latency_ms` sitting next to a much larger
`slow_operations` entry is expected given these are different
populations, not a contradiction.

Every `recent_errors` entry always carries an `error_code`: a real
Contextual error code when the failure was typed, or the literal
string `"uncoded (real error, not yet mapped to a Contextual error
code)"` when it wasn't — that string still means a genuine error
happened, not that this tool malfunctioned.

## When to use it (and when not to)

Call it when you need evidence of what's actually happened recently
before diagnosing or explaining a problem. Skip it if you want a live
point-in-time check instead of history — use `get_doctor` — or want
both correlated into one ranked diagnosis — use `diagnose_issue`.

## See also

- `cli/reference/general/activity` — the CLI equivalent (audit-trail side only).
- `mcp/tools/reference/get_doctor`, `mcp/tools/reference/diagnose_issue`.
- `observability/explanation/the-audit-log`.
