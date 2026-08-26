---
title: "client"
domain: cli
category: reference
tldr: "contextual client --<name> wires the MCP connection for the current workspace into one AI client's own config file. Exactly one client flag per call — --dry-run previews the write without making it."
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

<Callout variant="warning">
Exactly one client flag is required per call — passing more than one
(e.g. `--claude-code --cursor` together) is rejected with an error, not
configured together. Connecting several clients to the same workspace
means running the command once per client.
</Callout>

## What each client flag actually does

Every client except two gets an MCP server entry written into that
client's own config file (`claude_desktop_config.json`, `.mcp.json`,
`.cursor/mcp.json`, etc.). For most of those, that's the *only* thing
written per-repo — tool-usage guidance for Claude Code, Cursor, GitHub
Copilot, Codex, and Antigravity ships as a separate, device-wide Agent
Skill instead (`contextual skill --<name>`, installed once per machine,
not per repo — see `cli/reference/skill/skill`). Claude Desktop and
Gemini CLI have no skill mechanism either; for those two, the command
prints a one-line hint to copy `.contextual/AGENTS.md` into that
client's own custom-instructions setting yourself.

A few clients are handled differently:

- **`--chatgpt`, `--perplexity`** — both are UI-configured, desktop-app
  clients with no local MCP config file Contextual can write to. This
  flag instead prints the exact server name/command to paste into that
  app's own connector settings UI, plus the specific menu path to find
  it.
- **`--cline`** — Cline has no separate MCP config file at all; this
  flag injects Contextual's tool-usage block straight into the
  workspace's `AGENTS.md`.
- **`--windsurf`** — the one client that gets both a config entry *and*
  a per-repo `.windsurfrules` injection in the same step, because
  Windsurf doesn't support global/device-wide skills (see
  `cli/reference/skill/skill`).

`contextual client rm` always removes the config entry, and also
clears a per-repo markdown block for the client if one exists —
`.windsurfrules`/`AGENTS.md` genuinely get written by `--windsurf`/
`--cline` above, but for the skill-based clients this is defensive
cleanup of a file nothing writes today (`.cursor/rules/contextual.mdc`,
`.agent/rules/contextual.md`, `CLAUDE.md`), not a sign that `client
--<name>` itself created it.

Every successful configure also mints a scoped access token for that
(client, workspace) pair in the global `clients.json` access policy —
see `cli/reference/client/client-config` and
`mcp/server/explanation/mcp-client-access-control`.

## See also

- `cli/reference/client/client-rm`, `cli/reference/client/client-list`,
  `cli/reference/client/client-config`.
- `integrations/tutorials/connecting-an-ai-client`.
