---
title: "skill"
domain: cli
category: reference
tldr: "contextual skill [--client] installs a device-wide Agent Skill (SKILL.md) for one or all supported clients — Claude Code, Cursor, Codex, Copilot, Antigravity, Cline. Separate from client, which wires the actual MCP connection per repo."
order: 21
---

<Callout variant="tldr">
`contextual skill` installs a global Agent Skill file for your AI
client(s) — once per device, not per repository. With no flags, it
installs for every supported client at once; pass one client flag to
target just that one.
</Callout>

## Usage

```
contextual skill [--claude-code] [--cursor] [--codex] [--copilot]
                  [--antigravity] [--cline] [--dry-run]
```

- `--claude-code`, `--cursor`, `--codex`, `--copilot`, `--antigravity`,
  `--cline` — install for just that one client. Only one may be given
  per invocation.
- `--dry-run` — show what would be written without writing it.

<Terminal lines={[
  {command: "contextual skill --claude-code"},
  {output: "Wrote ~/.claude/skills/contextual/SKILL.md", muted: true}
]} />

<Callout variant="note">
This is device-wide, not per-repository — it's a different concern from
`contextual client`, which wires the MCP connection itself for one
workspace at a time. Windsurf isn't in this list on purpose: it only
supports project-scoped skills today, so Windsurf gets its skill
content through `contextual client --windsurf` instead.
</Callout>

## See also

- `cli/reference/skill-rm`.
- `cli/reference/client/client`.
