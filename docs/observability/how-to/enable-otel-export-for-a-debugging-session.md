---
title: Enable OTel export for a debugging session
domain: observability
category: how-to
tldr: "export_to_lancedb is on by default, feeding get_telemetry/diagnose_issue/contextual activity — here's how to confirm it's active, and how to turn it off if you don't want the local telemetry overhead."
order: 3
related:
  - observability/reference/observability-configuration-reference.md
  - observability/reference/logs-and-retention-reference.md
  - mcp/tools/reference/get_telemetry.md
---

<Callout variant="tldr">
`export_to_lancedb` ships on by default — there's no separate step to
"turn on" telemetry before a debugging session, since `get_telemetry`,
`diagnose_issue`, and `contextual activity` all depend on it already
running. This page covers confirming it's active, and disabling it if
you'd rather not pay the local storage cost.
</Callout>

## Confirming it's active

Open the global config (`contextual config --global`) and check the
`[observability]` section for `export_to_lancedb` — its absence means
the default (`true`) is in effect, not that it's off. The faster check
is functional: call `get_telemetry` and look at `trace_data_available`
in the response — `false` means nothing has been exported yet for the
requested window (a very fresh daemon, or export genuinely disabled),
not that the tool itself failed.

## Turning it off

Set `export_to_lancedb = false` under `[observability]` in the global
config, then `contextual mcp restart` — both config files are read
once at daemon startup, not live-reloaded (see
`configuration/reference/configuration-reference`). With it off,
`otel_spans`/`otel_logs` stop receiving new rows: `get_telemetry` and
`diagnose_issue` still run, but their trace-derived fields
(`slow_operations`, trace-sourced `recent_errors`) come back empty —
`contextual activity`'s audit-trail-based figures (call counts, error
rate, latency, token cost) are unaffected either way, since that data
comes from the separate, always-on `audit_log` (see
`observability/explanation/the-audit-log`).

Reasons to actually do this: you don't want the local storage overhead
of continuously-recorded spans/logs, or you're on a workspace where
you'd rather not have tool-call detail persisted even locally, even
though it's never sent anywhere off your machine either way (see
`trust-and-privacy/explanation/what-stays-local`).

## Getting more out of it for a specific session

`trace_sample_rate` already defaults to `1.0` (every operation traced,
no sampling) — there's no "turn it up" step. If you want faster
visibility into a change you're actively debugging, lower
`export_flush_interval_ms` (default 5000) so batched spans/logs land
sooner instead of waiting for the next scheduled flush.

## See also

- `observability/reference/observability-configuration-reference` — every field, with defaults.
- `mcp/tools/reference/get_telemetry`, `mcp/tools/reference/diagnose_issue`.
- `observability/reference/logs-and-retention-reference`.
