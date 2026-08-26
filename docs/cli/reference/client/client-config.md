---
title: "client config"
domain: cli
category: reference
tldr: "contextual client config opens the global clients.json access-policy file in your editor, creating an empty one first if it doesn't exist yet."
order: 30
---

<Callout variant="tldr">
`contextual client config` opens `clients.json` — the global file that
controls per-client access policy — in your default editor. If it
doesn't exist yet, an empty one is created first.
</Callout>

## Usage

```
contextual client config
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual client config"},
  {output: "Opening ~/.contextual/clients.json in $EDITOR...", muted: true}
]} />

## See also

- `cli/reference/client/client-list`.
- `mcp/server/explanation/mcp-client-access-control`.
