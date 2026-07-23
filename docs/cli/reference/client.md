---
title: "client"
domain: cli
category: reference
tldr: "contextual client --<name> wires the MCP connection for the current workspace into one AI client's own config file. Multiple client flags can be combined in one call, and --dry-run previews the write."
order: 27
---

<Callout variant="tldr">
`contextual client --<name>` writes (or updates) the MCP server entry
for the current workspace into that client's own config file. Run it
once per client you use, from inside the repository you want it
connected to.
</Callout>

## Usage

```
contextual client [--claude] [--claude-code] [--chatgpt] [--perplexity]
                   [--gemini-cli] [--cursor] [--copilot] [--windsurf]
                   [--codex] [--antigravity] [--cline] [--dry-run]
```

- `--claude` — Claude Desktop.
- `--claude-code` — Claude Code (CLI).
- `--chatgpt` — ChatGPT.
- `--perplexity` — Perplexity.
- `--gemini-cli` — Gemini CLI.
- `--cursor` — Cursor IDE.
- `--copilot` — GitHub Copilot (VS Code).
- `--windsurf` — Windsurf.
- `--codex` — Codex CLI.
- `--antigravity` — Antigravity.
- `--cline` — injects tool instructions into `AGENTS.md` rather than a
  client config file.
- `--dry-run` — show what would be written without writing it.

<Terminal lines={[
  {command: "contextual client --claude-code"},
  {output: "Configured Claude Code for this workspace.", muted: true}
]} />

Multiple client flags can be passed in one call to configure several
clients at once, from the same workspace.

## See also

- `cli/reference/client-rm`, `cli/reference/client-list`,
  `cli/reference/client-config`.
- `cli/tutorials/connecting-an-ai-client`.
