# Observability

Logs, the doctor report, and what's really instrumented via OpenTelemetry today.

- `observability/explanation/how-tracing-and-logging-work` — what's
  actually instrumented (indexing, retrieval, MCP) and how structured
  logging and OpenTelemetry export relate to each other.
- `observability/explanation/the-audit-log` — the always-on, SOC2-style
  tool-call audit trail, and how it differs from ephemeral telemetry.
- `observability/how-to/interpreting-doctor-report` — reading
  `contextual doctor`'s seven independent checks.
- `observability/how-to/read-and-interpret-daemon-and-cli-logs` —
  which log file to check for which kind of problem.
- `observability/how-to/enable-otel-export-for-a-debugging-session` —
  confirming OTel export is active (it's on by default), and turning
  it off.
- `observability/reference/logs-and-retention-reference` — file
  locations, rotation, and database retention windows.
- `observability/reference/observability-configuration-reference` —
  every `[observability]` config.toml field.
