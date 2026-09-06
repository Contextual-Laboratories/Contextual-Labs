---
title: "co_change_analysis"
domain: mcp-tools
category: reference
tldr: "co_change_analysis(entity_id, limit, include_weak, include_receipts, gcf, workspace) — finds entities that historically change together with a given one across commits, surfacing undeclared coupling that structural edges (calls/imports) miss entirely."
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
- `include_weak` (boolean, default false) — a partner's real coupling
  strength (Jaccard similarity: shared commits over the union of both
  entities' commits) can be near zero even when its raw co-change *count*
  clears the minimum-commits gate — such partners are classified
  `weak_historical_association` and excluded by default. Pass `true` to
  see them anyway.
- `include_receipts` (boolean, default false) — include each partner's
  actual shared commits (`commit_hash`/`author`/`committed_at`, capped at
  10 per partner) instead of just the count, so you can verify a
  coupling rather than take the number on faith.
- `gcf` (boolean, default false).
- `workspace` (string, optional).

Each returned partner is flagged as **declared** (also structurally
connected) or **undeclared** (coupled in history only) — the
undeclared ones are the actually useful signal here. Each is also
classified `undeclared_coupling` (real signal, above the coupling-strength
floor) or `weak_historical_association` (near-zero real coupling despite
a nonzero raw count) — treat the latter as noise, not a dependency.

`_meta.temporal_confidence` carries the same `{degraded, reasons}` shape
used across every history-derived tool (see `get_temporal_context`) —
check it before trusting coupling data from a shallow clone.

## When to use it (and when not to)

Call it before a refactor, to catch surprise blast radius structural
analysis alone would miss. Skip it if you need literal diff text
(`get_git_diff`) or declared structural dependencies (`graph_traverse`).

## See also

- `mcp/tools/reference/graph_impact`, `mcp/tools/reference/get_git_diff`.
- `mcp/tools/how-to/find-coupled-files-for-refactor-planning`.
