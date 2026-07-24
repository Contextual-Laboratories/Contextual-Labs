---
title: "mcp stop"
domain: cli
category: reference
tldr: "contextual mcp stop stops the running background daemon. If nothing is running, it says so rather than erroring."
order: 10
---

<Callout variant="tldr">
`contextual mcp stop` stops the currently running daemon. If no daemon
is running, it tells you that directly instead of failing.
</Callout>

## Usage

```
contextual mcp stop
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual mcp stop"},
  {output: "Daemon (PID 41213) stopped.", muted: true}
]} />

## See also

- `cli/reference/mcp/mcp-start`, `cli/reference/mcp/mcp-restart`.
