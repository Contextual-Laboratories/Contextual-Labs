---
title: "decision_update"
domain: mcp-tools
category: reference
tldr: "decision_update(adr_id, action, workspace) — transitions an ADR's status: accept (proposed → accepted), deprecate (accepted → deprecated, retires it with no replacement), or reinstate (deprecated → accepted)."
order: 18
---

<Callout variant="tldr">
`decision_update` applies one of three status transitions to an existing
ADR: `accept` ratifies a proposed decision, making it active and
re-embedding it so `decision_search` picks it up; `deprecate` retires an
accepted decision that no longer applies with nothing replacing it (it
drops out of `decision_search` but still appears in `decision_list`);
`reinstate` undoes a deprecate, bringing a decision back to `accepted`
(e.g. a removed feature came back). An accept/reinstate that failed to
embed is reported explicitly, not silently assumed to have worked.
</Callout>

## Parameters

- `adr_id` (string, required) — ID of the ADR to update.
- `action` (string, required) — one of `"accept"`, `"deprecate"`, `"reinstate"`.
- `workspace` (string, optional).

The response includes the new `status`, an `embedded` field reflecting
the real, persisted state, and a `reconcile` result — not an optimistic
assumption that the transition always succeeds.

## When to use it (and when not to)

Call it to approve a proposed ADR, to retire an accepted decision that
no longer applies (nothing replacing it), or to bring back a previously
deprecated one. Skip it if the ADR doesn't exist yet (create it first
with `decision_create`), or a decision IS being replaced by a new one —
use `decision_supersede` instead of `deprecate`.

## See also

- `mcp/tools/reference/decision_create`, `mcp/tools/reference/decision_supersede`.
