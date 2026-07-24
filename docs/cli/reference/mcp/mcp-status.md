---
title: "mcp status"
domain: cli
category: reference
tldr: "contextual mcp status shows whether the background daemon is running, its PID, health, loaded models, and active sessions/workspaces."
order: 8
---

<Callout variant="tldr">
`contextual mcp status` reports whether the daemon is running and, if
so, its health-check result, PID, whether the embedding model is
loaded, and how many sessions/workspaces are currently active.
</Callout>

## Usage

```
contextual mcp status
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual mcp status"},
  {output: "Daemon PID: 41213 | Port: UDS\nStatus:     ok\nState:      ready\nModels:     loaded\nSessions:   1\nWorkspaces: 2", muted: true}
]} />

If no daemon is running at all, this prints `Daemon: NOT RUNNING (no
lockfile)` rather than an error — that's a normal, expected state before
your first `mcp start` or before any AI client has triggered one.

<Callout variant="note">
On macOS and Linux the daemon communicates over a Unix domain socket, so
"Port" shows `UDS` rather than a number — that's expected, not a
misconfiguration. Windows is the one platform that uses a real TCP port
here.
</Callout>

## See also

- `observability/how-to/interpreting-doctor-report` for the **Daemon &
  Locks** check `doctor` runs separately.
- `cli/reference/mcp/mcp-start`, `cli/reference/mcp/mcp-restart`.
