---
title: Cursor integration
domain: integrations
category: reference
tldr: "How to connect Cursor to Contextual. Auto-written JSON config (~/.cursor/mcp.json)."
order: 6
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --cursor` writes `~/.cursor/mcp.json`. Tool-usage
guidance is a separate, device-wide install — run `contextual skill
--cursor` once per machine, not per repo.
</Callout>

## Connecting

```
contextual client --cursor
```

<Terminal lines={[
  {command: "contextual client --cursor"},
  {output: "✓ Cursor  → ~/.cursor/mcp.json\n  entry: Contextual-MCP · restart Cursor to activate\n\nTool-usage instructions now ship as a Skill, not a per-repo file — run: contextual skill --cursor", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP` in `~/.cursor/mcp.json`,
pointing at the `contextual-mcp` binary with `--workspace` set to this
repo. This file is shared across workspaces; re-running from another
repo adds a distinctly-keyed second entry rather than overwriting this
one.

Tool-usage instructions ship as a device-wide Agent Skill, not a
per-repo file — run `contextual skill --cursor` (or let `contextual
setup` do it automatically) to install
`~/.cursor/skills/contextual/SKILL.md`; see `cli/reference/skill/skill`.

## Removing

```
contextual client rm --cursor
```

Removes this workspace's entry from `~/.cursor/mcp.json`, and clears
`.cursor/rules/contextual.mdc` in this repo if that file exists. The
device-wide skill file is separate — remove it explicitly with
`contextual skill rm --cursor` if you want that gone too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`, `cli/reference/skill/skill-rm`.
