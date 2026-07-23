---
title: "decision_list"
domain: mcp-server
category: reference
tldr: "decision_list(status, limit, workspace) — a lightweight listing of ADRs with an optional status filter; the way to see what decisions exist, or get an adr_id, before calling accept/supersede/search."
order: 20
---

<Callout variant="tldr">
`decision_list` shows what ADRs exist — id, title, status, author, and
supersession links — without a semantic search step.
</Callout>

## Parameters

- `status` (string, optional) — filter to one status, e.g.
  `"proposed"`, `"accepted"`, `"superseded"`.
- `limit` (integer, 1–100, default 20).
- `workspace` (string, optional).

## When to use it (and when not to)

Call it for "what decisions have been made" or to get an `adr_id`
before calling `decision_accept`/`decision_supersede`. Skip it if you
need to find an ADR by topic or content — use `decision_search`
instead.

## See also

- `mcp-server/reference/decision_search`, `mcp-server/reference/decision_accept`.
