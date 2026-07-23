---
title: Reset config to defaults
domain: cli
category: how-to
tldr: Run contextual config reset (add --global for the global config) — your workspace's identity is preserved, so existing indexed data doesn't get orphaned.
order: 5
---

<Callout variant="tldr">
`contextual config reset` restores tunable settings to defaults after a
confirmation prompt. It never re-mints your workspace's identity, so
data you've already indexed stays correctly associated with it.
</Callout>

```
contextual config reset
```

<Terminal lines={[
  {command: "contextual config reset"},
  {output: "Are you sure you want to reset the workspace configuration to defaults? [y/N]: y\nWorkspace configuration has been reset to defaults.", muted: true}
]} />

Add `--global` to reset the global config instead of the current
workspace's.

## See also

- `cli/reference/config-reset`, `cli/reference/config`.
- `cli/explanation/config-precedence`.
