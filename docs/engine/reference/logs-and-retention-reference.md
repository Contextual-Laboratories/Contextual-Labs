---
title: Logs & Retention Reference
domain: engine
category: reference
tldr: Two log files, both rotated, both bounded — plus a 7-day retention pruner that only ever touches ephemeral telemetry tables, never your indexed content, embeddings, or blame cache, which persist until you explicitly re-index or delete them.
order: 4
related:
  - engine/how-to/read-and-interpret-daemon-and-cli-logs.md
  - engine/reference/storage-schema-reference.md
  - engine/reference/data-privacy.md
---

<Callout variant="tldr">
Log files rotate and are bounded in size. Database retention is narrower
than "everything expires after 7 days" — the pruner only ever touches
ephemeral telemetry and audit tables. Your actual indexed content
(embeddings, graph, blame cache) has no expiry at all; it persists until
you re-index or delete it yourself.
</Callout>

## Log files

| File | Contents | Rotation |
|---|---|---|
| `~/.contextual/daemon.log` | Everything the daemon process writes to stdout/stderr, including its own structured log output | Capped at 10MB; rotated to a single `.1` backup, both at daemon spawn and periodically while the daemon is running (not only at spawn) |
| `~/.local/state/contextual/logs/contextual.log` | The same structured JSON log stream, written via a dedicated handler | 10MB per file, 5 backups (~60MB total) via standard log rotation |
| `~/.local/state/contextual/logs/index.log` | Detailed per-stage indexing trace | Developer/source-install use — not written on a standard packaged install |

`contextual.log` writing is controlled by the `log_to_file` setting (on
by default). See `engine/how-to/read-and-interpret-daemon-and-cli-logs`
for which file to check for which kind of problem.

`contextual mcp logs [--follow] [--lines N]` tails `daemon.log`
specifically — see `cli/reference/mcp-logs.md`.

## Database retention — narrower than it sounds

A background pruner runs at daemon startup and on a recurring interval
(every 24 hours by default), deleting rows older than a retention window
from exactly two kinds of table:

- **Ephemeral telemetry** (`otel_spans`, `otel_logs`) — 7 days by
  default.
- **The tool-call audit trail** (`audit_log`) — 90 days by default, a
  deliberately longer, separate window from telemetry, since an audit
  trail and throwaway performance telemetry don't belong to the same
  retention policy just because a shared background loop happens to
  sweep both.

<Callout variant="warning">
This is the single easiest thing to get wrong about Contextual's storage:
retention applies **only** to the two categories above. It does **not**
apply to your indexed content — embeddings, chunks, the dependency graph,
blame cache, or ADRs. None of that has any automatic expiry. It persists
indefinitely until you explicitly `contextual index --force` (which
re-embeds and replaces it) or delete the workspace yourself. Don't assume
a "7-day retention" policy means your index quietly ages out — it
doesn't.
</Callout>

## OTel span/log export is opt-in, not on by default

Contextual's tracing instrumentation is real (a genuine OpenTelemetry
SDK, not a stub), but writing spans/logs to the local database tables
that the retention pruner above manages is off by default — there's
currently no built-in way to read that data back out, so writing it by
default would be pure storage cost for no payoff. Turn it on only if
you're actively debugging and want local trace data to inspect.

## What gets swept on `contextual uninstall`

Uninstalling cleans up more than just the daemon: indexing trace logs and
their rotated backups, and `daemon.log`'s rotated backup, are removed as
part of a full uninstall — see `cli/reference/uninstall.md`.
