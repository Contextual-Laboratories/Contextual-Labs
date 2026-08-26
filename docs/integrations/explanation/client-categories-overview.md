---
title: Client categories overview
domain: integrations
category: explanation
tldr: "The three real integration mechanisms across all 11 supported clients: auto-written config, Markdown injection, and UI-only manual setup."
order: 4
related:
  - integrations/reference/claude-desktop.md
  - integrations/reference/windsurf.md
  - integrations/reference/cline.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
`contextual client --<name>` connects a client through exactly one of
three mechanisms: writing an MCP config entry, injecting a Markdown
block into a shared instructions file, or (for two UI-only clients)
printing what to paste in yourself. One client, Windsurf, genuinely
does both of the first two in a single step.
</Callout>

## The three mechanisms

**Auto-written config** — the MCP connection itself is a JSON or TOML
entry written directly into that client's own config file, no manual
editing required: Claude Desktop, Claude Code, Gemini CLI, Cursor,
GitHub Copilot, Windsurf, Codex, and Antigravity all work this way.
The exact format differs (VS Code's `mcp.json` uses a `servers` root
key instead of `mcpServers`; Codex uses TOML instead of JSON), but the
underlying shape is identical: a command that launches
`contextual-mcp`, scoped to one workspace.

**Markdown injection** — a versioned, marker-delimited block written
directly into a shared instructions file, rather than a separate
config entry. Windsurf gets this *in addition to* its MCP config entry
(injected into `.windsurfrules`); Cline gets *only* this — there's no
separate MCP config file for Cline at all, the `AGENTS.md` block is the
entire connection.

**UI-only manual setup** — ChatGPT and Perplexity have no local config
file Contextual can write to at all. `contextual client --chatgpt`/
`--perplexity` print the server name and launch command for you to
paste into that app's own Connectors UI, and still mint a scoped access
token for the workspace — the token side of the connection is identical
to every other client, only the config-writing side is manual.

## Tool-usage guidance is a separate, cross-cutting concern

Whether a client also gets *tool-usage instructions* (what Contextual's
tools do, when to reach for them) is independent of the three
mechanisms above:

- Claude Code, Cursor, GitHub Copilot, Codex, and Antigravity get this
  through a device-wide Agent Skill (`contextual skill --<name>`,
  installed once per machine, not per repo) — see
  `cli/reference/skill/skill`.
- Windsurf and Cline get it folded into the same Markdown block their
  connection step already writes — Windsurf doesn't support a
  device-wide skills directory, so its per-repo injection carries this
  content instead.
- Claude Desktop, ChatGPT, Perplexity, and Gemini CLI get neither
  automatically — the connection command prints a one-line hint to
  copy `.contextual/AGENTS.md`'s content into that client's own
  project/account-level custom-instructions setting yourself.

## See also

- `integrations/reference/windsurf` — the one client combining config + injection.
- `integrations/reference/cline` — injection with no config file at all.
- `cli/reference/skill/skill` — the device-wide instruction mechanism most clients actually use.
