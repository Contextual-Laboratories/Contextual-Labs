---
title: Codex integration
domain: integrations
category: reference
tldr: "How to connect Codex to Contextual. Auto-written TOML config (~/.codex/config.toml)."
order: 9
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --codex` writes `~/.codex/config.toml` — the one
JSON-based-elsewhere client here that actually uses TOML, matching
Codex's own config format. Tool-usage guidance is a separate,
device-wide skill install.
</Callout>

## Connecting

```
contextual client --codex
```

<Terminal lines={[
  {command: "contextual client --codex"},
  {output: "✓ Codex  → ~/.codex/config.toml\n  entry: Contextual-MCP · restart Codex to activate\n\nTool-usage instructions ship as a Skill — run: contextual skill --codex", muted: true}
]} />

## What gets written

A `[mcp_servers."Contextual-MCP"]` TOML table in `~/.codex/config.toml`,
pointing at the `contextual-mcp` binary with `--workspace` set to this
repo. This file is shared across workspaces; re-running from another
repo adds a distinctly-keyed second table rather than overwriting this
one.

Tool-usage instructions ship as a device-wide Agent Skill — run
`contextual skill --codex` (or let `contextual setup` do it
automatically) to install `~/.codex/skills/contextual/SKILL.md`; see
`cli/reference/skill/skill`.

## Removing

```
contextual client rm --codex
```

Removes this workspace's table from `~/.codex/config.toml`. The
device-wide skill file is separate — remove it explicitly with
`contextual skill rm --codex` if you want that gone too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`, `cli/reference/skill/skill-rm`.
