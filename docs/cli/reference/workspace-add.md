---
title: "workspace add"
domain: cli
category: reference
tldr: "contextual workspace add <path> [--name NAME] registers a repository in the multi-workspace registry so MCP tools can address it by name."
order: 14
---

<Callout variant="tldr">
`contextual workspace add <path>` registers a repository as a named
workspace. Needed when you work across more than one repository and
want your AI client to be able to address a specific one by name rather
than always operating on "the current directory."
</Callout>

## Usage

```
contextual workspace add <path> [--name NAME]
```

- `path` (required, positional) — absolute path to the workspace root.
- `--name`, `-n` — human-readable name for this workspace. Defaults to
  the directory's basename if omitted.

<Terminal lines={[
  {command: "contextual workspace add /Users/you/dev/api-core --name api-core"},
  {output: "Registered workspace 'api-core'\n/Users/you/dev/api-core", muted: true}
]} />

## See also

- `cli/reference/workspace-list`, `cli/reference/workspace-rm`.
- `cli/tutorials/working-across-multiple-workspaces`.
