---
title: "workspace rm"
domain: cli
category: reference
tldr: "contextual workspace rm <name> deregisters a workspace by name — it removes the registry entry only, it does not delete the repository or its .contextual/ data."
order: 15
---

<Callout variant="tldr">
`contextual workspace rm <name>` removes a workspace from the registry
by its registered name. This only affects the registry entry — your
repository and its `.contextual/` data are untouched.
</Callout>

## Usage

```
contextual workspace rm <name>
```

- `name` (required, positional) — the registered workspace name, as
  shown by `contextual workspace list`.

<Terminal lines={[
  {command: "contextual workspace rm docs-site"},
  {output: "Removed workspace 'docs-site'", muted: true}
]} />

A name that doesn't match any registered workspace fails with an
explicit error rather than doing nothing silently.

## See also

- `cli/reference/workspace/workspace-list`, `cli/reference/workspace/workspace-add`.
