---
title: MCP Daemon Lifecycle & Hot-Reload
domain: mcp-server
category: explanation
tldr: The daemon has no in-place code reload — restart means stop then start, full stop — but it does hot-reload the workspace registry and client-access config on a SIGHUP, so a code change and a config change need genuinely different responses.
order: 9
related:
  - getting-started/explanation/architecture-overview.md
  - mcp/server/how-to/restart-the-daemon-after-a-code-or-config-change.md
  - troubleshooting/daemon-not-responding.md
---

<Callout variant="tldr">
"Restart the daemon" and "the daemon picked up my config change
automatically" are two different mechanisms, not the same thing worded
differently. Code changes always require a full `contextual mcp restart`.
Workspace registry and `mcp.json` client-access changes hot-reload on
their own — no restart needed.
</Callout>

## There is no in-place code reload

`contextual mcp restart` is literally stop, then start — there's no
lighter-weight in-place reload path. If you've edited anything in
Contextual's own pipeline or MCP code (relevant if you're running from
source, not a packaged install), the running daemon is still serving the
old code until it's actually restarted.

**Stop** sends `SIGTERM` and waits up to several seconds for a graceful
shutdown — long enough for the file watcher and any in-flight work to
tear down cleanly — before escalating to `SIGKILL` if it hasn't exited.
**Start** spawns the daemon process, waits on its `/live` endpoint until
it reports ready — the embedding model itself typically loads in a few
seconds — and only then hands control back to you. The wait is bounded by
a configurable startup timeout (`[daemon].startup_timeout`, default 15
seconds); if startup is consistently slower than that on your machine,
it's a knob you can raise rather than something to work around.

<Callout variant="warning">
Don't kill the daemon process by hand (`kill -9`, Activity Monitor, Task
Manager) as a first move if it seems stuck — `contextual mcp restart`
achieves the same result without leaving a stale lockfile behind for the
next start attempt to trip over.
</Callout>

## What *does* hot-reload: workspace registry and client access

The daemon listens for `SIGHUP` and, on receipt, reloads its in-memory
workspace registry and re-reads `mcp.json` (your client access policies —
see `mcp/server/explanation/mcp-client-access-control`) without a full
restart. This is what lets `contextual workspace add`, `contextual client
config`, and similar commands take effect against an already-running
daemon without interrupting an active session.

## The rule of thumb

| You changed... | What you need |
|---|---|
| Contextual's own pipeline/MCP source code | `contextual mcp restart` (full stop + start) |
| A workspace registration (`workspace add`/`remove`) | Nothing — hot-reloads automatically |
| Client access policy (`mcp.json` / `contextual client config`) | Nothing — hot-reloads automatically |
| Global or workspace `config.toml` (indexing/retrieval tuning) | `contextual mcp restart` — this is read at process startup, not on SIGHUP |

## A related but separate idea: model memory management

After a period of idleness, the daemon may unload the embedding model
from memory to free RAM and transparently reload it on the next call that
needs it. This is memory management, not hot-reload — it has nothing to
do with code changes and doesn't require or trigger a restart. See
`models/explanation/embedding-model-stack` for more on this.

## Checking daemon health

`contextual mcp status` tells you whether the daemon is running at all.
For a fuller picture — process alive, actually listening, lockfile
consistent — `contextual doctor`'s **Daemon & Locks** check is the
authoritative source; see
`observability/how-to/interpreting-doctor-report`. If the daemon seems up
but isn't responding to tool calls, see
`troubleshooting/daemon-not-responding`.
