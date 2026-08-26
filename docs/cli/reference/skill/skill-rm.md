---
title: "skill rm"
domain: cli
category: reference
tldr: "contextual skill rm --<client> removes the globally installed Agent Skill for exactly one client — a client flag is required, there's no remove-all shortcut."
order: 22
---

<Callout variant="tldr">
`contextual skill rm --<client>` removes the Agent Skill installed for
one specific client. Unlike `skill` itself, this requires a client flag
— there's no "remove for everyone" shortcut, by design.
</Callout>

## Usage

```
contextual skill rm --claude-code | --cursor | --codex | --copilot
                     | --antigravity | --cline
```

Exactly one client flag is required. Omitting all of them prints the
full list of valid flags and exits with an error rather than doing
nothing silently; passing more than one is also rejected — run the
command once per client.

<Terminal lines={[
  {command: "contextual skill rm --cursor"},
  {output: "Removed ~/.cursor/skills/contextual/SKILL.md", muted: true}
]} />

If nothing was installed for that client to begin with, `skill rm`
reports that plainly rather than treating it as an error — it's safe to
run against a client you're not sure has the skill installed.

## See also

- `cli/reference/skill/skill` — install, plus the full per-client path
  table.
