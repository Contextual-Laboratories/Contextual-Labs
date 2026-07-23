---
title: Working Across Multiple Workspaces
domain: cli
category: tutorial
tldr: Register each repository you work in with contextual workspace add, then specify which one you mean whenever more than one is registered.
order: 3
---

<Callout variant="tldr">
If you work across more than one repository, register each with
`contextual workspace add` and give each a clear name — an AI client can
then be told which workspace a question is about instead of guessing
from your current directory.
</Callout>

## 1. Register each repository

```
contextual workspace add /Users/you/dev/api-core --name api-core
contextual workspace add /Users/you/dev/docs-site --name docs-site
```

`--name` is optional — it defaults to the directory's basename — but an
explicit, memorable name is worth typing once.

## 2. Index each one

```
contextual index /Users/you/dev/api-core
contextual index /Users/you/dev/docs-site
```

## 3. See what's registered

```
contextual workspace list
```

<Terminal lines={[
  {command: "contextual workspace list"},
  {output: "NAME       PATH                        LAST INDEXED\napi-core   /Users/you/dev/api-core    2026-07-22 09:41 UTC\ndocs-site  /Users/you/dev/docs-site   2026-07-22 09:52 UTC", muted: true}
]} />

## 4. Asking about a specific workspace

Once more than one workspace is registered, tools won't guess which one
you mean if it's ambiguous — they'll ask you to specify. In practice
this means naming the workspace explicitly in your question to your AI
client ("in api-core, what calls...") rather than assuming context. See
`engine/troubleshooting/workspace-not-connecting` for exactly what each
of the three possible registry-related errors means if this goes wrong.

## Removing a workspace later

```
contextual workspace rm docs-site
```

This only removes the registry entry — it doesn't touch the repository
or its `.contextual/` directory.

## See also

- `cli/reference/workspace-add`, `cli/reference/workspace-list`,
  `cli/reference/workspace-rm`.
