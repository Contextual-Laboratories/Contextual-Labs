---
title: "workspace list"
domain: cli
category: reference
tldr: "contextual workspace list shows every registered workspace by name, folder, a shortened workspace ID, and when it was last indexed."
order: 13
---

<Callout variant="tldr">
`contextual workspace list` prints every workspace currently registered
on this machine — name, folder, a shortened workspace ID, and
last-indexed timestamp.
</Callout>

## Usage

```
contextual workspace list
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual workspace list"},
  {output: "name       folder      id            last indexed\napi-core   api-core    a1b2c3d4e5f6  2026-07-22 09:41 UTC\ndocs-site  docs-site   9f8e7d6c5b4a  never", muted: true}
]} />

The `folder` column is the repository's directory name, not its full
path — every row already has a human-chosen `name`, so the full
absolute path (visible via `contextual workspace add`'s own output, or
directly in `~/.contextual/workspaces.json`) isn't repeated here. `id`
is the workspace's ID truncated to 12 characters, matching what other
commands (like `contextual stats`) show.

An empty registry prints "No workspaces registered." rather than an
error or an empty table — that's the normal state before your first
`workspace add` (or before `init` has registered anything, on setups
where that happens automatically).

<Callout variant="note">
A workspace can appear here with no live `.contextual/` behind it — for
example if it was deleted by hand outside the CLI. `workspace list`
still shows it (with `last indexed` reading `missing` instead of a
timestamp) and prints a follow-up warning naming it, rather than
silently hiding it or crashing. Reinitialize it with `contextual init
<path>`, or deregister it with `contextual workspace rm <name>`.
</Callout>

## See also

- `cli/reference/workspace/workspace-add`, `cli/reference/workspace/workspace-rm`.
- `troubleshooting/workspace-not-connecting`.
