---
title: "workspace list"
domain: cli
category: reference
tldr: "contextual workspace list shows every registered workspace by name, path, workspace ID, and when it was last indexed."
order: 13
---

<Callout variant="tldr">
`contextual workspace list` prints every workspace currently registered
on this machine — name, path, workspace ID, and last-indexed timestamp.
</Callout>

## Usage

```
contextual workspace list
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual workspace list"},
  {output: "NAME       PATH                        LAST INDEXED\napi-core   /Users/you/dev/api-core    2026-07-22 09:41 UTC\ndocs-site  /Users/you/dev/docs-site   never", muted: true}
]} />

An empty registry prints an empty list, not an error — that's the
normal state before your first `workspace add` (or before `init` has
registered anything, on setups where that happens automatically).

## See also

- `cli/reference/workspace-add`, `cli/reference/workspace-rm`.
- `engine/troubleshooting/workspace-not-connecting`.
