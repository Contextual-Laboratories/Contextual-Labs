---
title: "mcp logs"
domain: cli
category: reference
tldr: "contextual mcp logs [--follow] [--lines N] tails the daemon's log file (last 50 lines by default) — use --follow to stream it live."
order: 12
---

<Callout variant="tldr">
`contextual mcp logs` prints the daemon's most recent log lines (50 by
default). Add `--follow` to stream new lines live, or `--lines N` to
change how many historical lines are shown.
</Callout>

## Usage

```
contextual mcp logs [--follow] [--lines N]
```

- `--follow`, `-f` — keep streaming new log lines as they're written,
  like `tail -f`.
- `--lines N`, `-n N` — number of historical lines to print. Defaults
  to 50.

<Terminal lines={[
  {command: "contextual mcp logs --lines 5"},
  {output: "2026-07-22T09:14:02Z INFO daemon_started pid=41213 port=0\n2026-07-22T09:14:02Z INFO models_loaded\n2026-07-22T09:41:18Z INFO mcp_tool_called tool=search\n2026-07-22T09:41:18Z INFO mcp_tool_ok tool=search duration_ms=84\n2026-07-22T10:02:07Z INFO workspace_registry_reloaded", muted: true}
]} />

If no log file exists yet at all, this tells you that directly rather
than failing silently.

## See also

- `observability/` for the full picture of what's logged and
  where.
- `mcp/server/how-to/read-mcp-logs-to-debug-a-broken-tool-call`.
