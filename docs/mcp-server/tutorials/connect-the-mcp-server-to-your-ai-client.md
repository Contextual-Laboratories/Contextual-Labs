---
title: Connect the MCP server to your AI client
domain: mcp-server
category: tutorial
tldr: contextual client --<name> writes a config entry pointing your AI client at the contextual-mcp binary for the current workspace — this is the mechanism, cli/tutorials/connecting-an-ai-client covers the command itself.
order: 1
---

<Callout variant="tldr">
Under the hood, connecting a client means writing an MCP server entry —
a launch command pointing at the `contextual-mcp` stdio binary, scoped
to one workspace — into that client's own config file. `contextual
client --<name>` does this for you; this page explains what actually
gets written and why.
</Callout>

## What gets written

Every supported client's config format is different (JSON for most,
TOML for Codex, a markdown block for Cline), but the shape is the same
idea: an entry that tells the client "here's a command to launch an MCP
server, and here's the workspace it should operate on." The workspace
is baked into the entry itself — it's why `contextual client` is a
per-workspace command, not a one-time global setup.

## Why this is a separate binary from `contextual`

The CLI you type (`contextual`) and the MCP server your AI client talks
to (`contextual-mcp`) are two different entry points to the same
installed package. Your client launches `contextual-mcp` as a
long-lived stdio process; you never invoke it directly yourself.

## Verifying the connection worked

```
contextual mcp status
```

<Terminal lines={[
  {command: "contextual mcp status"},
  {output: "Daemon PID: 41213 | Port: UDS\nStatus:     ok\nSessions:   1", muted: true}
]} />

A session count of at least 1 once your client has actually opened a
conversation confirms the connection is live, not just configured.

## See also

- `cli/tutorials/connecting-an-ai-client` — the command-line walkthrough.
- `mcp-server/tutorials/first-grounded-query`.
