---
title: "get_git_diff"
domain: mcp-tools
category: reference
tldr: "get_git_diff(base, target, file_path, workspace) — shows file-level changes between two git refs (or HEAD vs. the uncommitted working tree), truncated at 50KB."
order: 5
---

<Callout variant="tldr">
`get_git_diff` returns real diff text between two git refs, or between
`HEAD` and your uncommitted working tree — with per-file addition/
deletion counts and a changed-file list.
</Callout>

## Parameters

- `base` (string, default `"HEAD"`) — base git ref (a SHA, branch, tag,
  or relative ref like `HEAD~3`).
- `target` (string, default `"WORKING"`) — target git ref, or the
  literal `"WORKING"` for the uncommitted working tree.
- `file_path` (string, optional) — restrict the diff to one
  repo-relative file.
- `workspace` (string, optional).

## When to use it (and when not to)

Call it for "what changed since X," "show me the diff," or "what did
this commit touch." Skip it if you're looking for historical coupling
patterns across many commits — that's `co_change_analysis`.

## See also

- `mcp/tools/reference/co_change_analysis`.
- `mcp/tools/reference/get_temporal_context`.
