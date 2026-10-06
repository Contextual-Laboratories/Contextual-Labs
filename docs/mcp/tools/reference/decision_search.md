---
title: "decision_search"
domain: mcp-tools
category: reference
tldr: "decision_search(query, limit, filters, workspace) — hybrid semantic and keyword search over ADRs by topic, returning ranked decisions with full context, the decision text, and consequences."
order: 21
---

<Callout variant="tldr">
`decision_search` answers "why was X chosen" or "is there an ADR about
Y" — hybrid semantic and keyword search over recorded architectural
decisions, not a status filter.
</Callout>

## Parameters

- `query` (string, required) — natural-language topic or question.
- `limit` (integer, 1–20, default 5).
- `filters` (object, optional) — reserved for forward compatibility;
  currently a no-op.
- `workspace` (string, optional).

Each result's `match_type` says how it was found. `"semantic"` means
it was ranked by embedding similarity and has a real `score`. `"lexical"`
means an exact or near-exact keyword match in the ADR's title or decision
text rescued it when the embedding missed, which matters because the
embedding model is tuned for code and can under-rank plain-prose
decisions. A lexical result's `score` is `null` rather than an invented
number. A query with a lexical hit is never rejected as "no confident
match," and `WEAK_MATCH_SIGNAL` in `stale_warnings` is computed from the
semantic scores only. Results can include ADRs still in `proposed`
status; check each one's `status` before treating it as settled.
Deprecated and superseded ADRs never appear here (use `decision_list`).

Each returned ADR carries `relative_time` ("3 days ago") alongside its
exact `valid_at` timestamp, and the response's `_meta.temporal_confidence`
carries the same `{degraded, reasons}` shape used across every
history-derived tool (see `get_temporal_context`) — check it before
trusting a match found in a shallow clone.

## When to use it (and when not to)

Call it for architectural-reasoning questions about past design
choices. Skip it if you want to list all ADRs or filter by status — use
`decision_list` — or you're searching code behavior, not decisions —
use `search`.

## See also

- `mcp/tools/reference/decision_list`, `mcp/tools/reference/search`.
