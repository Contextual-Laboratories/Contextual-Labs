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
  per invocation — passing two at once fails with an error rather than
  installing to both.
- `--dry-run` — print the target path and the first ~400 characters of
  the `SKILL.md` that would be written, without writing anything.

With no flags at all, it installs to every supported client in one
pass, in a fixed order (Claude Code, Cursor, Codex, Copilot,
Antigravity, Cline) — this is also exactly what `contextual setup` does
for you automatically as part of first-time install (see
`cli/reference/general/setup`), so running this command by hand is only
needed to add a client you skipped, or to re-sync `SKILL.md` after an
engine upgrade changed its content.

<Terminal lines={[
  {command: "contextual skill --claude-code"},
  {output: "Wrote ~/.claude/skills/contextual/SKILL.md\n\nSome clients need a restart or window reload to pick up a newly installed skill.", muted: true}
]} />

## Where each client's skill file goes

Every path below is under your home directory and is written
identically regardless of which repository you ran the command from —
there is no per-workspace variant.

| Client | Path |
|---|---|
| Claude Code | `~/.claude/skills/contextual/SKILL.md` |
| Cursor | `~/.cursor/skills/contextual/SKILL.md` |
| Codex CLI | `~/.codex/skills/contextual/SKILL.md` |
| GitHub Copilot | `~/.copilot/skills/contextual/SKILL.md` |
| Antigravity | `~/.gemini/config/skills/contextual/SKILL.md` |
| Cline | `~/.cline/skills/contextual/SKILL.md` |

<Callout variant="note">
This is device-wide, not per-repository — it's a different concern from
`contextual client`, which wires the MCP connection itself for one
workspace at a time. Windsurf isn't in this list on purpose: as of this
writing it only supports project-scoped skills, not a global skills
directory, so Windsurf gets equivalent tool-usage guidance injected
per-repo through `contextual client --windsurf` instead — there's no
`contextual skill --windsurf` flag to reach for.
</Callout>

<Callout variant="note">
Installing a skill does not check whether that client actually has
Contextual wired up over MCP yet (`contextual client`). Running
`contextual skill --cursor` before `contextual client --cursor` leaves
Cursor able to see the skill's guidance with no matching tools to act
on it — harmless, but not useful until the MCP connection exists too.
</Callout>

## See also

- `cli/reference/skill/skill-rm`.
- `cli/reference/client/client` — the per-repo MCP connection this skill
  content assumes is already wired up.
- `cli/reference/general/setup` — installs the skill for every client
  automatically as part of first-time setup.
