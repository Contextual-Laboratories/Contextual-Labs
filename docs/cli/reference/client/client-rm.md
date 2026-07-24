---
title: "client rm"
domain: cli
category: reference
tldr: "contextual client rm --<name> removes this workspace's MCP entry from that client's config — UI-based clients like ChatGPT and Perplexity have nothing local to remove."
order: 28
---

<Callout variant="tldr">
`contextual client rm --<name>` removes the current workspace's entry
from that client's config file. Same flag set as `contextual client`.
</Callout>

## Usage

```
contextual client rm [--claude] [--claude-code] [--cursor] [--copilot]
                      [--gemini-cli] [--chatgpt] [--perplexity] [...]
```

Same client flags as `contextual client`. `--chatgpt` and
`--perplexity` are UI-based clients — there's nothing local for either
of them to remove, and the command tells you that rather than pretending
to do something.

<Terminal lines={[
  {command: "contextual client rm --cursor"},
  {output: "Removed Cursor MCP entry and .cursor/rules/contextual.mdc.", muted: true}
]} />

## See also

- `cli/reference/client/client`.
