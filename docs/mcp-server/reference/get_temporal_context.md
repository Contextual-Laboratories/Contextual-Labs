---
title: "get_temporal_context"
domain: mcp-server
category: reference
tldr: "get_temporal_context(entity_id, window_days, workspace) — commit history, git blame, change velocity, and linked ADRs for one entity, to understand who changed it, how often, and why."
order: 22
---

<Callout variant="tldr">
`get_temporal_context` answers "who wrote this," "how often does this
change," and "what commits touched this" — recent commits, blame,
change-velocity, and any linked ADRs, for one entity.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to fetch history
  for.
- `window_days` (integer, 1–365, default 30) — lookback window for
  recent commits.
- `workspace` (string, optional).

A high change-velocity result is a real risk signal worth surfacing
before editing a hot path, not just trivia.

## When to use it (and when not to)

Call it for authorship/change-frequency questions about a specific
entity. Skip it if you want raw diff text — use `get_git_diff` — or
need broad index freshness — use `get_stats`.

## See also

- `mcp-server/reference/get_git_diff`, `mcp-server/reference/decision_search`.
