---
title: "graph_impact"
domain: mcp-tools
category: reference
tldr: "graph_impact(entity_id, change_type, limit, include_code, gcf, workspace) — blast-radius analysis: everything that breaks if this entity is deleted, renamed, or has its signature changed. The mandatory pre-flight check before any of those three changes."
order: 12
---

<Callout variant="tldr">
`graph_impact` is the mandatory check before deleting, renaming, or
changing the signature of anything — it returns every entity that
breaks as a result, ranked by hop distance.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to analyze.
- `change_type` (`"delete"` | `"rename"` | `"signature_change"`, default
  `"delete"`) — controls which edge types and traversal depth are used.
- `limit` (integer, 1–50, default 30).
- `include_code` (boolean, default false).
- `gcf` (boolean, default false).
- `workspace` (string, optional).

<Callout variant="warning">
For `change_type="rename"`, the response also includes a
`speculative_callers` list — low-confidence (`potential_call`, 0.35)
edges. Treat these as advisory only, never as confirmed dependents. The
`_meta._resolution_summary` breaks down the impact estimate by
confidence tier for the same reason: a high speculative count inflates
the apparent blast radius and should not drive a refactoring decision
without manual verification.
</Callout>

## Why this matters

Reading "0 impacted" from this tool as "confirmed safe to delete" is
only valid once you know the entity itself was found — see
`troubleshooting/entity-not-found` for the distinction between
"genuinely zero dependents" and "the graph doesn't know this entity
exists."

## When to use it (and when not to)

Call it before any deletion, rename, or API-breaking change. Skip it if
you want to see what an entity itself uses (its outgoing edges) — use
`graph_traverse` with `direction="forward"` instead.

## See also

- `mcp/tools/reference/graph_traverse`, `mcp/tools/reference/graph_get_entity_callers`.
- `mcp/tools/how-to/run-blast-radius-analysis-before-a-rename-or-delete`.
