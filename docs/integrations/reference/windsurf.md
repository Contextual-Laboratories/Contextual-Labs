---
title: Windsurf integration
domain: integrations
category: reference
tldr: "How to connect Windsurf to Contextual. Auto-written JSON config + .windsurfrules injection."
order: 8
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
---

<Callout variant="tldr">
`contextual client --windsurf` is the one client that writes both
pieces automatically in a single step: the MCP entry, and tool-usage
instructions injected straight into `.windsurfrules`. Windsurf doesn't
support the device-wide skill mechanism other clients use, so this is
where its equivalent guidance lives instead.
</Callout>

## Connecting

```
contextual client --windsurf
```

<Terminal lines={[
  {command: "contextual client --windsurf"},
  {output: "✓ Windsurf  → ~/.codeium/windsurf/mcp_config.json\n  entry: Contextual-MCP · restart Windsurf to activate\n\n  md:  .windsurfrules  · appended", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP` in
`~/.codeium/windsurf/mcp_config.json`, plus a versioned, marker-delimited
block appended to this repo's `.windsurfrules` file with tool-usage
guidance. The marker block coexists with anything else already in
`.windsurfrules` — your own rules aren't touched — and a `.bak` copy is
taken the first time the block is injected, so pre-Contextual content
is always recoverable.

Windsurf is deliberately handled differently from Cursor/Copilot/Codex/
Antigravity/Claude Code here: those five get their tool-usage guidance
through a separate, device-wide `contextual skill` install instead of a
per-repo file, but Windsurf only supports project-scoped skills today —
not a global skills directory — so it keeps the per-repo
`.windsurfrules` injection as its equivalent mechanism.

## Removing

```
contextual client rm --windsurf
```

Removes the MCP entry from `~/.codeium/windsurf/mcp_config.json` and
the Contextual marker block from `.windsurfrules` — only the marked
section, not the whole file, so any other rules you've written stay
intact.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill` — why other clients don't need this per-repo step.
