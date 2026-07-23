---
title: "co_change_analysis"
domain: mcp-server
category: reference
tldr: "co_change_analysis(entity_id, limit, gcf, workspace) — finds entities that historically change together with a given one across commits, surfacing undeclared coupling that structural edges (calls/imports) miss entirely."
order: 16
---

<Callout variant="tldr">
`co_change_analysis` finds hidden dependencies — files or entities that
change together in the same commits even though nothing in the code
structurally links them. This is the "what else changes when I touch
X?" question, and it catches coupling `graph_traverse` structurally
cannot see.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to find coupling
  for.
- `limit` (integer, 1–30, default 10).
- `gcf` (boolean, default false).
- `workspace` (string, optional).

Each returned partner is flagged as **declared** (also structurally
connected) or **undeclared** (coupled in history only) — the
undeclared ones are the actually useful signal here.

## When to use it (and when not to)

Call it before a refactor, to catch surprise blast radius structural
analysis alone would miss. Skip it if you need literal diff text
(`get_git_diff`) or declared structural dependencies (`graph_traverse`).

## See also

- `mcp-server/reference/graph_impact`, `mcp-server/reference/get_git_diff`.
- `mcp-server/how-to/find-coupled-files-for-refactor-planning`.
