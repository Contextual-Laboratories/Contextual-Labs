---
title: Observability configuration reference
domain: observability
category: reference
tldr: "Every ObservabilityConfig field — logging, tracing, export, and retention settings."
order: 2
related:
  - configuration/reference/configuration-reference.md
  - observability/explanation/how-tracing-and-logging-work.md
  - observability/reference/logs-and-retention-reference.md
---

<Callout variant="tldr">
All of this lives under `[observability]` in the **global**
`~/.contextual/global_config.toml` — not the per-workspace config —
since logging/tracing/export behavior is a property of the daemon
process, not any one workspace.
</Callout>

## Logging

| Key | Default | What it controls |
|---|---|---|
| `log_level` | `"INFO"` | Log verbosity, applied to both console and file output. |
| `log_format` | `"console"` | `"console"` (human-readable) or `"json"`. |
| `log_to_file` | `true` | Whether `contextual.log` gets written at all. |
| `log_file_path` | `null` | Override the log file location; `null` uses the default state directory. |

## Tracing

| Key | Default | What it controls |
|---|---|---|
| `tracing_enabled` | `true` | Whether the OpenTelemetry SDK is initialized at all. |
| `trace_sample_rate` | `1.0` | Fraction of operations traced (root sampler); `1.0` = every operation, no sampling. |
| `service_name` | `"contextual"` | Reported on every span/log row's `service_name` field. |
| `service_version` | current package version | Reported alongside `service_name`. |

## Export

| Key | Default | What it controls |
|---|---|---|
| `export_to_lancedb` | `true` | Whether spans/logs are written to the `otel_spans`/`otel_logs` tables — on by default since `get_telemetry`/`diagnose_issue`/`contextual activity` depend on it. See `observability/how-to/enable-otel-export-for-a-debugging-session`. |
| `export_batch_size` | `100` | Max spans/logs per export batch. |
| `export_flush_interval_ms` | `5000` | How often a batch flushes even if not yet full. |

## Retention

| Key | Default | What it controls |
|---|---|---|
| `retention_days` | `7` | How long `otel_spans`/`otel_logs` rows are kept. |
| `audit_log_retention_days` | `90` | How long `audit_log` rows are kept — deliberately separate and longer; see `observability/explanation/the-audit-log`. |
| `prune_on_startup` | `true` | Whether a prune pass runs once when the daemon starts. |
| `prune_interval_hours` | `24` | How often the recurring background prune runs. |

## Instrumentation scope

| Key | Default | What it controls |
|---|---|---|
| `instrument_indexing` | `true` | Whether indexing pipeline stages get traced spans. |
| `instrument_retrieval` | `true` | Whether `search`/`nexus_search` get traced spans. |
| `instrument_mcp` | `true` | Whether every MCP tool call gets a `mcp.tool.*` span. |
| `instrument_storage` | `false` | Raw LanceDB reads/writes aren't traced individually today. |

## See also

- `configuration/reference/configuration-reference` — the rest of `global_config.toml` and workspace `config.toml`.
- `observability/explanation/how-tracing-and-logging-work` — what these flags actually produce.
- `observability/reference/logs-and-retention-reference` — file-level rotation, separate from this database-level retention.
