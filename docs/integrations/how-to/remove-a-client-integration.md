---
title: Remove a client integration
domain: integrations
category: how-to
tldr: Run contextual client rm --<name> to remove the workspace's MCP entry from that client's config — UI-based clients like ChatGPT have nothing local to remove.
order: 6
---

<Callout variant="tldr">
`contextual client rm --<name>` removes the current workspace's entry
from one client's config file.
</Callout>

```
contextual client rm --cursor
```

<Terminal lines={[
  {command: "contextual client rm --cursor"},
  {output: "Removed Cursor MCP entry and .cursor/rules/contextual.mdc.", muted: true}
]} />

<Callout variant="note">
`--chatgpt` and `--perplexity` are UI-based clients — there's nothing
local for either of them to remove, and the command tells you that
directly rather than pretending to do something.
</Callout>

## See also

- `cli/reference/client/client/client-rm`, `cli/reference/client/client`.
