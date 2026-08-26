---
title: Gemini CLI integration
domain: integrations
category: reference
tldr: "How to connect Gemini CLI to Contextual. Auto-written JSON config (~/.gemini/settings.json)."
order: 5
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
---

<Callout variant="tldr">
`contextual client --gemini-cli` writes an MCP entry into
`~/.gemini/settings.json` — a device-wide file, so the entry is keyed
and scoped to this workspace rather than one file per repo.
</Callout>

## Connecting

```
contextual client --gemini-cli
```

<Terminal lines={[
  {command: "contextual client --gemini-cli"},
  {output: "✓ Gemini CLI  → ~/.gemini/settings.json\n  entry: Contextual-MCP · restart Gemini CLI to activate\n\n  Copy tool instructions from .contextual/AGENTS.md into:\n  Gemini CLI → GEMINI.md (project context file)", muted: true}
]} />

## What gets written

An `mcpServers` entry keyed `Contextual-MCP` in `~/.gemini/settings.json`,
pointing at the `contextual-mcp` binary with `--workspace` set to this
repo. Since this file is shared across every workspace you connect
Gemini CLI to, re-running the command from a different repo adds a
second, distinctly-keyed entry rather than overwriting this one.

There's no automatic per-repo instructions file — the command prints a
hint to copy `.contextual/AGENTS.md`'s content into your project's
`GEMINI.md` file yourself, Gemini CLI's own project-context mechanism.

## Removing

```
contextual client rm --gemini-cli
```

Removes only this workspace's entry from `~/.gemini/settings.json`;
entries for other workspaces are untouched.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `integrations/explanation/client-categories-overview`.
