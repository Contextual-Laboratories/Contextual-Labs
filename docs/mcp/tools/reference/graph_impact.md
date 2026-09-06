---
title: "graph_impact"
domain: mcp-tools
category: reference
tldr: "graph_impact(entity_id, change_type, limit, include_code, no_preview, full_detail, gcf, workspace) — blast-radius analysis: everything that breaks if this entity is deleted, renamed, or has its signature changed. The mandatory pre-flight check before any of those three changes."
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
- `no_preview` (boolean, default false) — omit the short code preview
  entirely, instead of `include_code`'s default ~70-line snippet. Use it
  when you only need node identity/impact metadata.
- `full_detail` (boolean, default false) — by default, an entity already
  returned at full detail earlier in this session collapses to a short
  `{id, name, already_shown: true}` reference on repeat, to avoid
  re-paying the same token cost for overlapping calls (e.g. `graph_impact`
  then `graph_get_entity_callers` on overlapping entities). Pass
  `full_detail=True` to opt out for one call.
- `gcf` (boolean, default false).
- `workspace` (string, optional).

<Callout variant="warning">
For `change_type="rename"` **and** `change_type="signature_change"`, the
response also includes a `speculative_callers` list — low-confidence
(`potential_call`, 0.35) edges. Treat these as advisory only, never as
confirmed dependents. The `_meta._resolution_summary` breaks down the
impact estimate by confidence tier for the same reason: a high
speculative count inflates the apparent blast radius and should not
drive a refactoring decision without manual verification.
</Callout>

<Callout variant="note">
For `change_type="signature_change"`, `_meta` also carries a
`completeness` field (`"complete"` | `"partial"` | `"unknown"`) alongside
`confirmed_count`/`speculative_count` — a real count of what was
statically confirmed versus what's only speculative, so you can tell
"the graph found nothing" apart from "the graph found this much and
isn't sure there's more." Responses may also drop low-priority fields
(staleness, degree, edges, temporal, authorship, then commit data, in
that order) under a response-size budget before dropping whole nodes —
check `_meta.omitted_fields` if a node looks unexpectedly sparse.
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
