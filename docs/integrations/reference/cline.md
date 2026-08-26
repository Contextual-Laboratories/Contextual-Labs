---
title: Cline integration
domain: integrations
category: reference
tldr: "How to connect Cline to Contextual. Markdown-only — no MCP config file, injects into AGENTS.md."
order: 11
related:
  - cli/reference/client/client.md
  - cli/reference/client/client-rm.md
  - cli/reference/skill/skill.md
---

<Callout variant="tldr">
Cline is the one client here with no separate MCP config file at all —
`contextual client --cline` injects a versioned block straight into
`AGENTS.md`, and that's the entire connection mechanism.
</Callout>

## Connecting

```
contextual client --cline
```

<Terminal lines={[
  {command: "contextual client --cline"},
  {output: "  md:  AGENTS.md  · appended", muted: true}
]} />

## What gets written

A versioned, marker-delimited block appended to this repo's
`AGENTS.md`, containing the MCP launch command and tool-usage guidance
together — there's no separate JSON/TOML config step for Cline the way
there is for every other client in this list. The marker block coexists
with anything else already in `AGENTS.md`, and a `.bak` copy is taken
the first time the block is injected.

Cline also has a device-wide Agent Skill available
(`contextual skill --cline`, installing
`~/.cline/skills/contextual/SKILL.md`) — but unlike the JSON-config
clients, `client --cline` doesn't depend on it; the `AGENTS.md`
injection alone is a complete, working connection on its own.

## Removing

```
contextual client rm --cline
```

Removes only the Contextual marker block from `AGENTS.md` — any other
content in that file is untouched. If the file becomes empty as a
result, it's deleted rather than left as a stray empty file.

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `cli/reference/skill/skill`.
