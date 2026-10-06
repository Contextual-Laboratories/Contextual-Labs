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
  clears the minimum-commits gate. A partner whose undecayed
  `historical_strength` is below the floor (`co_change_min_jaccard_strength`,
  default `0.05`) is classified
  `weak_historical_association` and excluded by default. Pass `true` to
  see them anyway.
- `include_receipts` (boolean, default false) — include each partner's
  actual shared commits (`commit_hash`/`author`/`committed_at`, capped at
  10 per partner) instead of just the count, so you can verify a
  coupling rather than take the number on faith.
- `gcf` (boolean, default false) — return the compact encoding. Its
  `## stats` columns describe each entity itself (language, change count,
  velocity, staleness, live degrees); the per-pairing co-change figures
  (`co_change_count`, `historical_strength`, `classification`,
  `is_undeclared_coupling`) are in a separate `partner_coupling` block,
  keyed by scope.
- `workspace` (string, optional).

Each returned partner is flagged as **declared** (also structurally
connected) or **undeclared** (coupled in history only) — the
undeclared ones are the actually useful signal here. Independently, each
is also classified `strong_change_coupling` (real signal, above the
coupling-strength floor) or `weak_change_coupling` (near-zero real
coupling despite a nonzero raw count) — treat the latter as noise, not a
dependency. These two are separate axes: a partner can be both
structurally declared AND a strong statistical coupling at once, which is
two true facts, not a contradiction. A partner whose entity ID no longer
resolves in the graph is dropped from the response (see
`_meta.dangling_ref_count`) rather than shown with null fields.

"Declared" means a direct `calls`, `imports`, `inherits_from`,
`implements`, `references_type`, or `references_field_type` edge, or —
when the entity is a whole file — a `file_depends_on` edge (the file's
own symbol-level dependencies rolled up to one edge per target file).
Without that roll-up a file-level entity could never match a
symbol-level edge and every partner would have read as undeclared.

Two strength numbers appear on every partner, and they answer different
questions. `coupling_strength` is recency-decayed, so an old pairing
reads lower. `historical_strength` is the same Jaccard ratio computed on
the undecayed commit count, and it is what `classification` is decided
on. `co_change_partners` and `summary.strongest_coupling` are both
ranked by `historical_strength`, so an old-but-real coupling isn't
outranked, or cut by `limit`, by a recent-but-weak one. When
`coupling_strength` alone would have read as weak and
`historical_strength` carried the classification, the partner has a
`note` saying so.

`confidence_focal_to_partner` and `confidence_partner_to_focal` are a
separate, directional signal: the fraction of the focal entity's own
commits that also touched the partner, and the reverse. They are often
asymmetric (for example 20% one way and 36% the other) even though
`coupling_strength` is a single symmetric number. Use them for "how often
does touching X drag Y along," as opposed to "are X and Y coupled."

If the focal entity was renamed or refactored, its earlier identities'
co-change history is merged in, so a rename doesn't make a real coupling
look weak or absent. When that happens the response's `entity` block
carries `lineage_prior_entity_ids`, listing the prior identities that
were folded in. Whole-file renames are detected from git itself
(`git log -M` content similarity over the most recent 2,000 commits,
ignoring commits that touch more than 50 files), not from file names.

`_meta.temporal_confidence` carries the same `{degraded, reasons}` shape
used across every history-derived tool (see `get_temporal_context`) —
check it before trusting coupling data from a shallow clone.

<Callout variant="note">
An entity spanning 3 lines or fewer (a one-line constant, a short
accessor) never appears as a cross-entity pairing partner here — a
narrow entity like this inherits its containing file's *entire* commit
history for other purposes (see `get_temporal_context`), and treating
that inherited history as if it were coupling specific to the entity
itself would misattribute the file's whole change pattern to a few
lines of it. This only affects pairing in this tool; the entity's own
temporal signal elsewhere is unchanged.
</Callout>

<Callout variant="note">
When more partners exist than `limit` returned, the response leads with
a top-level `_status` sentence ("Showing 8 of 19 matches — …") before
any other field, instead of leaving `_meta.truncated` as a boolean you
had to already know to check.
</Callout>

## When to use it (and when not to)

Call it before a refactor, to catch surprise blast radius structural
analysis alone would miss. Skip it if you need literal diff text
(`get_git_diff`) or declared structural dependencies (`graph_traverse`).

## See also

- `mcp/tools/reference/graph_impact`, `mcp/tools/reference/get_git_diff`.
- `mcp/tools/how-to/find-coupled-files-for-refactor-planning`.
