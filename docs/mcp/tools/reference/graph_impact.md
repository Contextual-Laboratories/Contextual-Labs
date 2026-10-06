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
  Besides a raw hash or an FQN, a bare symbol name (`LanceDBConnector`)
  or a repo-relative file path (`contextual/storage/connection.py`) also
  resolves. If several entities share a bare name, the most-referenced
  one is chosen; use an FQN when you need a specific one. See
  `troubleshooting/entity-not-found`.
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
Every response carries `_meta.coverage`, which says how much of the
real answer you're looking at: `returned`, `has_more`, a `confidence` of
`"resolved"`, `"resolved_but_capped"` or `"unknown"`, and — when
something was held back — `capped_by` naming the cause (`limit`,
`hydration_cap`, `diversity_cap`, or `token_budget`) with a matching
`hint`. Only some causes are fixed by raising `limit`; the hint says
which. It replaces the older `completeness`, `truncated` and
`confirmed_count` fields, so the response can no longer claim to be both
complete and truncated. See `mcp/tools/explanation/reading-the-coverage-block`.
</Callout>

<Callout variant="note">
An impacted node whose edge carries metadata, such as `via: "raise"` for
an instantiation inside a raise statement or `via: "decorator"` for a
function that is decorated by the entity, shows it under
`edge_metadata`. The field is absent when the edge has none. Note that
the older `via_predicate` field is unrelated: it names the edge type that
connected the node.
</Callout>

<Callout variant="note">
`coverage.unresolved_count` appears when the resolver found call sites
it declined to bind (for example a call through an untyped parameter)
that look like calls into this entity: same name, same language, from
this entity's file or a file that depends on it. It is a heuristic
"possible callers" figure, never a confirmed count, and it never changes
`confidence`; `unresolved_count_capped: true` marks it as a lower bound.
`confidence: "unknown"` means nothing was confirmed, not that nothing
exists. For `change_type="rename"` and
`"signature_change"`, `coverage.speculative_supplement_count` counts the
entries in `speculative_callers`, which are kept out of `impacted_nodes`
and out of `confidence`.
</Callout>

<Callout variant="note">
Responses may drop low-priority node fields (commit data first, then
authorship, temporal, edges, degree, and staleness last) under a
response-size budget before dropping whole nodes — check
`_meta.omitted_fields` if a node looks unexpectedly sparse. When
`has_more` is true the response also leads with a top-level `_status`
sentence ("Showing 12 of 31 matches — …") before any other field.
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
- `mcp/tools/explanation/reading-the-coverage-block`.
- `mcp/tools/how-to/run-blast-radius-analysis-before-a-rename-or-delete`.
