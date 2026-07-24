---
title: Run the MCP server as a background daemon
domain: mcp-server
category: how-to
tldr: Use contextual mcp start/stop/restart/status to control the background daemon directly, instead of relying on an AI client to spawn it implicitly.
order: 3
---

<Callout variant="tldr">
The daemon usually starts implicitly the first time an AI client needs
it. Use `contextual mcp start`/`stop`/`restart`/`status` when you want
direct control — for example, warming it up ahead of time, or forcing a
clean restart.
</Callout>

```
contextual mcp start
contextual mcp status
contextual mcp stop
contextual mcp restart
```

<Terminal lines={[
  {command: "contextual mcp start"},
  {output: "Daemon started.", muted: true},
  {command: "contextual mcp status"},
  {output: "Daemon PID: 41213 | Port: UDS\nStatus:     ok\nModels:     loaded", muted: true}
]} />

## See also

- `cli/reference/mcp/mcp-start`, `cli/reference/mcp/mcp-stop`,
  `cli/reference/mcp/mcp-restart`, `cli/reference/mcp/mcp-status`.
- `mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload`.
