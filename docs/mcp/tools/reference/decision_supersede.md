---
title: "decision_supersede"
domain: mcp-tools
category: reference
tldr: "decision_supersede(old_adr_id, new_adr_id, reason, workspace) — links two existing ADRs so the new one replaces the old, tombstoning the old record on both sides."
order: 19
---

<Callout variant="tldr">
`decision_supersede` links a replacement ADR to the one it replaces —
both records get updated, and the old one is tombstoned rather than
deleted outright.
</Callout>

## Parameters

- `old_adr_id` (string, required) — ID of the ADR being replaced.
- `new_adr_id` (string, required) — ID of the ADR that replaces it.
- `reason` (string, optional) — free-text reason for the supersession.
- `workspace` (string, optional).

Both ADRs must already exist. Superseding drops the old ADR's
embedding; the returned `reconcile` result confirms the surviving
(accepted) successor stays indexed.

## When to use it (and when not to)

Call it when a prior decision is being replaced and the new ADR already
has an `adr_id`. Skip it if the replacement doesn't exist yet — create
it first with `decision_create`.

## See also

- `mcp/tools/reference/decision_create`, `mcp/tools/reference/decision_accept`.
