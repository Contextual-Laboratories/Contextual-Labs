---
title: Claude Desktop integration
domain: integrations
category: reference
tldr: "How to connect Claude Desktop to Contextual. Auto-written JSON config (claude_desktop_config.json)."
order: 1
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - integrations/tutorials/connecting-an-ai-client.md
---

<Callout variant="tldr">
`contextual client --claude` writes an MCP server entry into Claude
Desktop's own config file — no manual JSON editing required.
</Callout>

## Connecting

```
contextual client --claude
```

<Terminal lines={[
  {command: "contextual client --claude"},
  {output: "✓ Claude Desktop  → ~/Library/Application Support/Claude/claude_desktop_config.json\n  entry: Contextual-MCP · restart Claude Desktop to activate\n\n  Copy tool instructions from .contextual/AGENTS.md into:\n  Claude → Project → Custom Instructions", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP`, pointing at the installed
`contextual-mcp` binary with `--workspace <this repo>` baked in — scoped
to the workspace you ran the command from. Config path:
`~/Library/Application Support/Claude/claude_desktop_config.json` on
macOS (platform-equivalent path on Windows/Linux).

Claude Desktop has no per-repo instructions file of its own — the
command prints a hint to copy `.contextual/AGENTS.md`'s content into
Claude's Project → Custom Instructions setting yourself, since that's
account/project-scoped in Claude Desktop's own UI, not something
Contextual can write to directly.

Restart Claude Desktop after connecting — the config file is only read
at launch.

## Removing

```
contextual client rm --claude
```

Removes the `Contextual-MCP` entry for this workspace and revokes its
scoped access token. Other workspaces' entries in the same file (if
you've connected Claude Desktop to more than one) are untouched.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `integrations/explanation/client-categories-overview`.
