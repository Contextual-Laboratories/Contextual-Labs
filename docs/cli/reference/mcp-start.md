---
title: "mcp start"
domain: cli
category: reference
tldr: "contextual mcp start launches the background daemon if it isn't already running, and waits until it's actually serving before returning."
order: 9
---

<Callout variant="tldr">
`contextual mcp start` starts the background daemon and waits for it to
be fully up before the command returns. If a daemon is already running,
it says so and does nothing further — it never starts a second one.
</Callout>

## Usage

```
contextual mcp start
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual mcp start"},
  {output: "Daemon started.", muted: true}
]} />

## See also

- `cli/reference/mcp-status`, `cli/reference/mcp-stop`,
  `cli/reference/mcp-restart`.
