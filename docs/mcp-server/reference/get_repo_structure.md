---
title: "get_repo_structure"
domain: mcp-server
category: reference
tldr: "get_repo_structure(path, depth, include_hidden, workspace) — returns the live directory tree, capped at 5 levels deep, to understand file/folder layout before navigating to a specific file."
order: 4
---

<Callout variant="tldr">
`get_repo_structure` returns a nested directory tree with file sizes and
total file/directory counts — for browsing layout, not for finding a
specific symbol.
</Callout>

## Parameters

- `path` (string, default `"."`) — repo-relative directory to root the
  tree at.
- `depth` (integer, 1–5, default 3) — max directory depth to descend.
- `include_hidden` (boolean, default false) — include dotfiles/
  dot-directories.
- `workspace` (string, optional).

## When to use it (and when not to)

Call it when the question is "what's in this repo," "what files
exist," or you need to verify a path before navigating to it. Skip it
if you're looking for a specific symbol or function definition — use
`search` instead.

## See also

- `mcp-server/reference/search`, `mcp-server/reference/get_file_content`.
