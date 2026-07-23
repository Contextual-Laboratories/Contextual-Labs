---
title: "mcp restart"
domain: cli
category: reference
tldr: "contextual mcp restart stops then starts the daemon in one step — the command to run after a Contextual code/config change that the daemon needs to pick up."
order: 11
---

<Callout variant="tldr">
`contextual mcp restart` stops and then starts the daemon in one
command. Use this after anything that requires the daemon to re-read
state — see the note below for exactly when that's actually necessary.
</Callout>

## Usage

```
contextual mcp restart
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual mcp restart"},
  {output: "Daemon (PID 41213) stopped.\nDaemon started.", muted: true}
]} />

<Callout variant="note">
The daemon does not hot-reload every kind of change automatically. If
you're actively developing against Contextual itself or debugging why a
recent change doesn't seem to be taking effect, `mcp restart` is the
command that forces a fresh process — don't assume the daemon has
already picked up a change without it.
</Callout>

## See also

- `cli/reference/mcp-start`, `cli/reference/mcp-stop`,
  `cli/reference/mcp-status`.
- `engine/how-to/restart-the-daemon-after-a-code-or-config-change`.
