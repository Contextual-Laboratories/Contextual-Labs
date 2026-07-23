---
title: "client list"
domain: cli
category: reference
tldr: "contextual client list shows every client registered in the global access policy, its access level, and which workspaces it's scoped to."
order: 29
---

<Callout variant="tldr">
`contextual client list` prints every client name known to the global
MCP access policy, its access level, and which workspaces it's allowed
to reach.
</Callout>

## Usage

```
contextual client list
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual client list"},
  {output: "NAME          ACCESS   WORKSPACES\nclaude-code   full     *\ncursor        full     api-core", muted: true}
]} />

## See also

- `cli/reference/client-config` — where access levels are actually set.
- `engine/explanation/mcp-client-access-control`.
