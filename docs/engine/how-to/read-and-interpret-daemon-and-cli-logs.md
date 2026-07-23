---
title: Read and Interpret Daemon and CLI Logs
domain: engine
category: how-to
tldr: contextual mcp logs tails the daemon's raw output; ~/.local/state/contextual/logs/contextual.log has the structured, per-tool-call JSON detail with latency and outcome for every MCP request.
order: 4
related:
  - engine/reference/logs-and-retention-reference.md
  - engine/observability/interpreting-doctor-report.md
  - engine/troubleshooting/daemon-not-responding.md
---

<Callout variant="tldr">
Two log files matter day to day: `~/.contextual/daemon.log` for the raw
daemon process output, and `~/.local/state/contextual/logs/contextual.log`
for structured, per-tool-call detail (which tool, which client, how long
it took, whether it succeeded). Reach for the second one when you need to
know *why* a specific tool call behaved the way it did.
</Callout>

## The fast path: `contextual mcp logs`

<Terminal lines={[
  {command: "contextual mcp logs --follow"},
  {output: "[daemon] Starting MCP server on 127.0.0.1:8420...\n[daemon] Model warmup complete (6.2s)\n[daemon] Ready.", muted: true}
]} />

This tails `~/.contextual/daemon.log` — a direct capture of everything
the daemon process writes to stdout/stderr, including its structured log
lines and anything else that isn't routed through the application logger
(startup banners, an uncaught traceback). It's the right first stop for
"did the daemon start correctly," "is it crashing," or "what did it
print right before it died." `--lines N` limits how far back it reads;
`--follow` streams new output live.

## The detailed path: `contextual.log`

`~/.local/state/contextual/logs/contextual.log` carries the same
structured JSON log lines as `daemon.log` — including a per-tool-call
summary for every MCP request (which tool, which client, a trace ID,
`latency_ms`, `outcome`) — written through a dedicated handler with a
longer retention window and without anything else the daemon process
might print mixed in. Reach for this one over `daemon.log` when you want
to grep or parse tool-call history specifically, without noise from
startup banners or unrelated process output.

<Callout variant="note">
Every log line here also carries the OpenTelemetry trace/span ID that
produced it, if tracing was active for that call — useful for correlating
a specific slow or failed tool call across multiple log lines, not just
reading them in isolation.
</Callout>

## Which one to check, by symptom

| Symptom | Check |
|---|---|
| Daemon won't start, or `mcp status` shows not running | `contextual mcp logs` |
| A specific tool call returned something unexpected | `contextual.log` — filter by tool name or trace ID |
| Indexing itself seems slow or stuck | See `engine/how-to/speed-up-or-debug-a-slow-index-run` first — its stage-name output usually tells you more directly than either log file |
| You don't know if the daemon is even healthy | `contextual doctor` — see `engine/observability/interpreting-doctor-report` |

## File locations and rotation, at a glance

Both files rotate rather than growing unbounded — see
`engine/reference/logs-and-retention-reference` for the exact rotation
behavior and how long each kind of data is kept. If you're trying to
correlate a problem to a specific timestamp, check rotation history
there before assuming an old log line is still on disk.
