---
title: Perplexity integration
domain: integrations
category: reference
tldr: "How to connect Perplexity to Contextual. UI-only — no local config file, added through the app's Connectors UI."
order: 4
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
---

<Callout variant="tldr">
Perplexity Desktop has no local MCP config file — `contextual client
--perplexity` prints the server name and launch command you paste into
its Connectors UI yourself, and still mints a scoped access token for
this workspace.
</Callout>

## Connecting

```
contextual client --perplexity
```

This prints setup steps rather than writing anything:

1. Open Perplexity Desktop (macOS) and go to Settings.
2. Navigate to Connectors → Add Connector.
3. Enter the server name and command shown.
4. Toggle MCP on under Sources on the homepage.

The server name to enter is `contextual`; the command is the
`contextual-mcp` binary invoked with `--workspace <this repo> --client
perplexity` — copy it exactly as printed.

A scoped access token for this workspace is minted the same as any
other client, even though there's no local config file to write it
into.

There's also a manual step for tool-usage context: copy
`.contextual/AGENTS.md`'s content into Perplexity's Space → System
Prompt, since that's the closest equivalent to per-project custom
instructions Perplexity exposes.

## Removing

```
contextual client rm --perplexity
```

There's no local file to remove — this revokes the scoped access token
only. Also turn MCP off inside Perplexity's own UI if you want the
connector gone from there too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `integrations/explanation/client-categories-overview`.
