---
title: GitHub Copilot (VS Code) integration
domain: integrations
category: reference
tldr: "How to connect GitHub Copilot (VS Code) to Contextual. Auto-written mcp.json using VS Code's 'servers' root key."
order: 7
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --copilot` writes `~/.vscode/mcp.json` — VS Code's
own MCP format, keyed `servers` rather than `mcpServers` like every
other JSON-based client here. Tool-usage guidance is a separate,
device-wide skill install.
</Callout>

## Connecting

```
contextual client --copilot
```

<Terminal lines={[
  {command: "contextual client --copilot"},
  {output: "✓ GitHub Copilot  → ~/.vscode/mcp.json\n  entry: Contextual-MCP · restart GitHub Copilot to activate\n\nTool-usage instructions now ship as a Skill, not a per-repo file — run: contextual skill --copilot\nWant a per-repo file instead? Copy .contextual/AGENTS.md into .github/copilot-instructions.md yourself.", muted: true}
]} />

## What gets written

An entry keyed `Contextual-MCP` under `servers` (not `mcpServers`) in
`~/.vscode/mcp.json` — VS Code requires an explicit `"type": "stdio"`
field on the entry, unlike the other clients here. It points at the
`contextual-mcp` binary with `--workspace` set to this repo.

Tool-usage instructions ship as a device-wide Agent Skill by default —
run `contextual skill --copilot` (or let `contextual setup` do it) to
install `~/.copilot/skills/contextual/SKILL.md`. If you'd rather have a
per-repo instructions file instead, the command tells you to copy
`.contextual/AGENTS.md` into `.github/copilot-instructions.md`
yourself — this isn't done automatically.

## Removing

```
contextual client rm --copilot
```

Removes this workspace's entry from `~/.vscode/mcp.json`, and clears
a Contextual block from `.github/copilot-instructions.md` in this repo
if one exists. The device-wide skill file is separate — remove it
explicitly with `contextual skill rm --copilot` if you want that gone
too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`, `cli/reference/skill/skill-rm`.
