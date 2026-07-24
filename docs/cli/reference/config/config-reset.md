---
title: "config reset"
domain: cli
category: reference
tldr: "contextual config reset [--global] restores defaults after a confirmation prompt — resetting a workspace config preserves its workspace ID, it never re-mints one."
order: 20
---

<Callout variant="tldr">
`contextual config reset` restores the workspace config to defaults,
after you confirm. Pass `--global` to reset the global config instead.
Resetting a workspace's config never changes its workspace ID — your
existing indexed data stays associated with it.
</Callout>

## Usage

```
contextual config reset [--global]
```

- `--global`, `-g` — reset the global config instead of the current
  workspace's config.

<Terminal lines={[
  {command: "contextual config reset"},
  {output: "Are you sure you want to reset the workspace configuration to defaults? [y/N]: y\nWorkspace configuration has been reset to defaults.", muted: true}
]} />

<Callout variant="note">
This resets tunable settings only — it deliberately preserves the
workspace's identity (its workspace ID and creation timestamp), since
those are what tie your existing indexed data in the database back to
this workspace. A reset that re-minted the ID would silently orphan
everything you'd already indexed.
</Callout>

## See also

- `cli/reference/config/config`.
