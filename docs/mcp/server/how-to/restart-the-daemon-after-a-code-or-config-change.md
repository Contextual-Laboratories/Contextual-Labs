---
title: Restart the Daemon After a Code or Config Change (and Know Which One You Actually Need)
domain: mcp-server
category: how-to
tldr: contextual mcp restart is required after any source-code or config.toml change; workspace registration and client-access changes hot-reload on their own and need nothing.
order: 3
related:
  - mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload.md
  - troubleshooting/daemon-not-responding.md
---

<Callout variant="tldr">
Run `contextual mcp restart` after editing Contextual's own source code
or `config.toml`. Don't run it for a workspace or client-access change —
those apply automatically, and restarting for them just costs you the
daemon's warm-up time for nothing.
</Callout>

## The command

<Terminal lines={[
  {command: "contextual mcp restart"},
  {output: "Daemon (PID 41213) stopped.\nDaemon started.", muted: true}
]} />

It's a full stop-then-start, not an in-place reload — see
`mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload` for why there's
no lighter-weight option for code changes.

## When you need it

- You edited Contextual's own pipeline/MCP source (running from source,
  not a packaged install).
- You edited `config.toml` — indexing or retrieval tuning is read once at
  process startup, not re-read live.

## When you don't

- `contextual workspace add`/`remove` — the daemon's workspace registry
  reloads on its own.
- `contextual client config` or any other edit to `mcp.json`'s
  client-access policies — same hot-reload path.

Restarting for either of these isn't harmful, just unnecessary — you'll
pay the daemon's startup cost (including reloading the embedding model)
for a change that would have applied anyway.

## If the restart doesn't seem to take effect

Confirm the daemon actually came back up before assuming your change
didn't apply:

<Terminal lines={[
  {command: "contextual mcp status"},
  {output: "Daemon: running (pid 41213, port 8420)", muted: true}
]} />

If status shows not running, or `contextual doctor`'s **Daemon & Locks**
check fails, see `troubleshooting/daemon-not-responding`.
