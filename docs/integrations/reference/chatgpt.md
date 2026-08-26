---
title: ChatGPT integration
domain: integrations
category: reference
tldr: "How to connect ChatGPT to Contextual. UI-only — no local config file, added through the app's Connectors UI."
order: 3
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
---

<Callout variant="tldr">
ChatGPT Desktop has no local MCP config file — `contextual client
--chatgpt` prints the server name and launch command you paste into
its Connectors UI yourself, and still mints a scoped access token for
this workspace.
</Callout>

## Connecting

```
contextual client --chatgpt
```

This prints setup steps rather than writing anything:

1. Open ChatGPT Desktop and go to Settings.
2. Navigate to Connectors → Advanced → Developer mode.
3. Click "Add server" and enter the name and command shown.
4. Toggle the server on and restart ChatGPT.

The server name to enter is `contextual`; the command is the
`contextual-mcp` binary invoked with `--workspace <this repo> --client
chatgpt` — copy it exactly as printed.

A scoped access token for this workspace is minted the same as any
other client, even though there's no local config file to write it
into — ChatGPT authenticates over the connection itself.

There's also a manual step for tool-usage context: copy
`.contextual/AGENTS.md`'s content into ChatGPT's Project → Custom
Instructions, since that's account/project-scoped in ChatGPT's own UI.

## Removing

```
contextual client rm --chatgpt
```

There's no local file to remove — this revokes the scoped access token
only. Also toggle the connector off inside ChatGPT's own UI if you want
it gone from there too; `client rm` can't reach into ChatGPT's UI
state.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `integrations/explanation/client-categories-overview`.
