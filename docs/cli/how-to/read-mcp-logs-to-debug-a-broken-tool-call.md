---
title: Read MCP logs to debug a broken tool call
domain: cli
category: how-to
tldr: Run contextual mcp logs --lines N to see recent daemon activity, or --follow to watch it live while you retry the failing call.
order: 4
---

<Callout variant="tldr">
`contextual mcp logs` tails the daemon's log file. Use `--follow` while
you re-trigger the failing tool call from your AI client, so you see
the relevant lines land in real time instead of scrolling back through
history.
</Callout>

```
contextual mcp logs --follow
```

<Terminal lines={[
  {command: "contextual mcp logs --follow"},
  {output: "2026-07-22T10:41:02Z INFO mcp_tool_called tool=graph_impact\n2026-07-22T10:41:02Z ERROR tool_graph_impact_failed error=\"entity not found\"", muted: true}
]} />

If the log shows an "entity not found" style error, see
`engine/troubleshooting/entity-not-found` for what that actually means
before assuming the tool itself is broken.

## See also

- `cli/reference/mcp-logs`.
- `engine/observability/`.
