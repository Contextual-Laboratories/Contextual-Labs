---
title: Antigravity integration
domain: integrations
category: reference
tldr: "How to connect Antigravity to Contextual. Auto-written JSON config (~/.gemini/config/mcp_config.json); tool-usage guidance is a separate device-wide skill install, not a per-repo file."
order: 10
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --antigravity` writes
`~/.gemini/config/mcp_config.json`. Tool-usage guidance is a separate,
device-wide skill install — nothing is injected into a per-repo file
automatically.
</Callout>

## Connecting

```
contextual client --antigravity
```

<Terminal lines={[
  {command: "contextual client --antigravity"},
  {output: "✓ Antigravity  → ~/.gemini/config/mcp_config.json\n  entry: Contextual-MCP · restart Antigravity to activate\n\nTool-usage instructions now ship as a Skill, not a per-repo file — run: contextual skill --antigravity\nWant a per-repo file instead? Copy .contextual/AGENTS.md into .agent/rules/contextual.md yourself.", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP` in
`~/.gemini/config/mcp_config.json`, pointing at the `contextual-mcp`
binary with `--workspace` set to this repo.

Tool-usage instructions ship as a device-wide Agent Skill — run
`contextual skill --antigravity` (or let `contextual setup` do it) to
install `~/.gemini/config/skills/contextual/SKILL.md`; see
`cli/reference/skill/skill`. If you'd rather have a per-repo
instructions file instead, the command tells you to copy
`.contextual/AGENTS.md` into `.agent/rules/contextual.md` yourself —
this isn't done automatically.

## Removing

```
contextual client rm --antigravity
```

Removes this workspace's entry from
`~/.gemini/config/mcp_config.json`, and clears
`.agent/rules/contextual.md` in this repo if that file exists. The
device-wide skill file is separate — remove it explicitly with
`contextual skill rm --antigravity` if you want that gone too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`, `cli/reference/skill/skill-rm`.
