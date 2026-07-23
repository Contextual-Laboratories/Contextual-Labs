---
title: "decision_accept"
domain: mcp-server
category: reference
tldr: "decision_accept(adr_id, workspace) — transitions an ADR from proposed to accepted, making it the active decision and re-embedding it so decision_search actually finds it."
order: 18
---

<Callout variant="tldr">
`decision_accept` ratifies a proposed ADR, making it the active design
decision. It also re-embeds the ADR so `decision_search` picks it up —
an accepted ADR that failed to embed is reported explicitly, not
silently assumed to have worked.
</Callout>

## Parameters

- `adr_id` (string, required) — ID of the ADR to accept.
- `workspace` (string, optional).

The response includes an `embedded` field reflecting the real,
persisted state, plus a `reconcile` result — not an optimistic
assumption that acceptance always succeeds.

## When to use it (and when not to)

Call it to approve an ADR previously created with `decision_create`.
Skip it if the ADR doesn't exist yet (create it first), or you're
replacing a decision with a new one entirely — use `decision_supersede`.

## See also

- `mcp-server/reference/decision_create`, `mcp-server/reference/decision_supersede`.
