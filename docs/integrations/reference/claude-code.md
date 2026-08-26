---
title: Claude Code integration
domain: integrations
category: reference
tldr: "How to connect Claude Code to Contextual. Auto-written per-workspace .mcp.json."
order: 2
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --claude-code` writes `.mcp.json` in the repo root.
Tool-usage guidance is a separate, device-wide install — run
`contextual skill --claude-code` once per machine, not per repo.
</Callout>

## Connecting

```
contextual client --claude-code
```

<Terminal lines={[
  {command: "contextual client --claude-code"},
  {output: "✓ Claude Code  → .mcp.json\n  entry: Contextual-MCP · restart Claude Code to activate\n\nTool-usage instructions now ship as a Skill, not a per-repo file — run: contextual skill --claude-code", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP` in `.mcp.json` at the repo
root, pointing at the `contextual-mcp` binary with `--workspace` set to
this repo. This file lives in the repository itself, not your home
directory — commit it or `.gitignore` it as you'd treat any other
local tooling config.

Tool-usage instructions (what Contextual's tools do and when to reach
for them) don't get written per-repo for Claude Code — they ship as a
device-wide Agent Skill instead. Run `contextual skill --claude-code`
once (or let `contextual setup` do it automatically on first install)
to install `~/.claude/skills/contextual/SKILL.md`; see
`cli/reference/skill/skill` for the full mechanism.

## Removing

```
contextual client rm --claude-code
```

Removes the `.mcp.json` entry for this workspace. The device-wide
skill file is separate and unaffected — remove it explicitly with
`contextual skill rm --claude-code` if you want that gone too.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`, `cli/reference/skill/skill-rm`.
