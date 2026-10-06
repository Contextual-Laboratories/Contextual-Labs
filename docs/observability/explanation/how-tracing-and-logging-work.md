---
title: How tracing and logging work
domain: observability
category: explanation
tldr: "What's actually instrumented (indexing, retrieval, MCP) and what isn't yet (storage), and where trace/log data goes."
order: 5
related:
  - observability/reference/observability-configuration-reference.md
  - observability/reference/logs-and-retention-reference.md
  - observability/explanation/the-audit-log.md
  - mcp/tools/reference/get_telemetry.md
---

<Callout variant="tldr">
Contextual runs a real OpenTelemetry SDK, not a stub — indexing,
retrieval, and MCP tool calls each get their own traced spans, and
structured log lines carry the trace/span ID of whatever call produced
them. Storage-layer calls (raw LanceDB reads/writes) aren't traced yet.
</Callout>

## What's actually instrumented

Four independent flags — `instrument_indexing`, `instrument_retrieval`,
`instrument_mcp`, `instrument_storage` — gate tracing per subsystem. The
first three default to on; `instrument_storage` defaults to off, so
individual LanceDB table reads/writes don't each produce their own
span today. A traced operation looks like `mcp.tool.search` or
`mcp.tool.nexus_search` — one span per MCP tool call, wrapping the
tool's actual work — with `workspace_id` and other operation-specific
attributes attached.

## Two independent systems, easy to conflate

**Structured logging** always runs, regardless of tracing config —
every log line is a structlog event, rendered to your terminal in dev
mode and always written as JSON to `contextual.log` (see
`observability/how-to/read-and-interpret-daemon-and-cli-logs`). When a
log line is emitted from inside a traced span, it automatically picks
up that span's `trace_id`/`span_id`, letting you correlate a specific
log line back to the operation that produced it.

**OpenTelemetry export** is a separate, additional layer: spans and
certain log lines are also written to two ephemeral LanceDB tables,
`otel_spans` and `otel_logs`, so that data can be queried back later —
by `get_telemetry`/`diagnose_issue` (MCP tools) and `contextual
activity` (CLI). This is controlled by `export_to_lancedb`, on by
default; see `observability/reference/observability-configuration-reference`.
Turning tracing off (`tracing_enabled: false`) or export off
(`export_to_lancedb: false`) doesn't touch structured logging at all —
`contextual.log` keeps working either way.

## One trace per indexing run, across two processes

A bulk `contextual index` runs in its own background process, but the work
it needs from the daemon (embedding) happens in the daemon. Both sides join
**one** trace:

- The indexing job opens a root span, `index.job`, with its phases
  (`index.phase.dynamic_index`, `index.phase.deep_index`) as children, and
  records the trace ID in its job state.
- Every embed request the job makes to the daemon carries the standard W3C
  `traceparent` header (and the workspace). The daemon's `internal.embed`
  span is a *child* of the job's span, with the batch size, queue wait and
  compute time as attributes.
- The job exports its spans to the workspace's own `otel_spans` table, and its
  log lines to `otel_logs` (each carrying the active span's trace and span ID),
  the same places the daemon's go for that workspace, so one query shows the
  whole run.

Every span, metric and log row names the process that produced it
(`service.name` = `contextual-daemon`, `contextual-index-job` or
`contextual-cli`) along with standard OpenTelemetry resource attributes
(`service.namespace`, `service.version`, `service.instance.id`,
`process.pid`, host architecture, OS).

Coordination facts are recorded as named events inside the trace they belong
to: `lease.acquired` / `lease.released` / `lease.reclaimed` (the machine-wide
model lease), `embed.backend.selected`, `background.deferred` /
`background.resumed` (daemon work that stepped aside for a bulk job), and
`power.sleep_guard.acquired` / `.released`.

## Metrics

Operational metrics use the standard OpenTelemetry metrics API:
`contextual.embed.queue_wait` and `contextual.embed.compute` (histograms),
`contextual.embed.chunks`, `contextual.background.deferred`,
`contextual.git.subprocess.duration` and `contextual.model_lease.held`. Labels
are deliberately coarse (kind, actor, reason, verb) — never file paths or
workspace IDs. Current values for the running daemon appear in
`get_telemetry`'s `process_metrics`; they are in-memory and reset when the
daemon restarts. To send traces and metrics to your own OpenTelemetry
collector as well, set `otlp_endpoint` — see
`observability/reference/observability-configuration-reference`. It is off by
default.

## Where each kind of data lands, and why per-workspace matters

The daemon serves multiple workspaces out of one process. A span or
log line tagged with a `workspace_id` attribute is routed to that
workspace's own LanceDB — not a single shared table — falling back to
the global database only for daemon-lifecycle events that have no
workspace of their own. This is why `get_telemetry`/`contextual
activity` only ever show you your own workspace's activity, not every
workspace the daemon happens to be serving.

Because the OTel SDK's batch span processor flushes from its own
background thread (not the daemon's asyncio event loop), the span
exporter captures the daemon's actual running event loop once at
startup and hands off every write to it explicitly, rather than
assuming whatever thread calls `export()` has a usable event loop of
its own — the log exporter runs on the event loop thread already, so
it doesn't need this.

## See also

- `observability/reference/observability-configuration-reference` — every instrumentation and export flag.
- `observability/reference/logs-and-retention-reference` — file locations, rotation, and database retention.
- `observability/explanation/the-audit-log` — the separate, always-on tool-call audit trail.
- `mcp/tools/reference/get_telemetry`, `mcp/tools/reference/diagnose_issue`.
