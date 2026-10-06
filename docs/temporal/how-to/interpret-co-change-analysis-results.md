---
title: Interpret co-change analysis results
domain: temporal
category: how-to
tldr: "Reading co_change_analysis output for refactor planning."
order: 5
related:
  - mcp/tools/reference/co_change_analysis.md
  - nexus/explanation/nexus-signal-sources.md
  - graph/explanation/the-knowledge-graph.md
---

<Callout variant="tldr">
`co_change_analysis` ranks partners by `historical_strength`, a
query-invariant fraction — not a raw commit count — and flags each one
`is_undeclared_coupling: true` when nothing structural connects it to
your focal entity. The undeclared ones are the actual signal worth
acting on.
</Callout>

## The shape of a result

```
{
  "entity": { "entity_id": ..., "name": ..., "entity_type": ..., "scope": ... },
  "co_change_partners": [
    {
      "entity_id": ..., "name": ..., "entity_type": ..., "scope": ...,
      "co_change_count": 14,
      "coupling_strength": 0.42,
      "historical_strength": 0.47,
      "confidence_focal_to_partner": 0.70,
      "confidence_partner_to_focal": 0.58,
      "has_structural_dependency": false,
      "is_undeclared_coupling": true,
      "classification": "strong_change_coupling"
    },
    ...
  ],
  "summary": {
    "total_partners": ...,
    "undeclared_couplings": ...,
    "strongest_coupling": { "entity_id": ..., "name": ..., "coupling_strength": ..., "historical_strength": ... }
  }
}
```

## Reading `historical_strength` and `coupling_strength`

Both are Jaccard-style ratios, not `co_change_count` itself. The one
that matters for judging whether a coupling is real is
`historical_strength`:

```
historical_strength = co_change_count / (focal_changes + partner_changes − co_change_count)
```

— the fraction of either entity's total changes that landed in a
commit shared with the other. `co_change_count` is the real historical
commit count and the totals are each entity's all-time distinct commit
counts. This is deliberately normalized against each entity's own total
change history rather than against the strongest partner in this one
result set: normalizing against the local max would make the number hit
`1.0` for every partner whenever they all happen to tie on raw count,
even when none of them are actually dominant. A value close to `1.0`
means these two entities essentially always change together; a low one
means they've coincidentally shared a few commits without a real
pattern.

`coupling_strength` is the same ratio computed from the pairing's
recency-decayed weight instead of the raw count, so an old pairing reads
lower than a recent one even if it was once very strong. It is shown
for context. `classification`, the ranking of `co_change_partners`, and
`summary.strongest_coupling` are all decided on `historical_strength`,
so an old-but-real coupling isn't outranked by a recent-but-weak one.
When `coupling_strength` alone would have read as weak while
`historical_strength` carried the classification, the partner has a
`note` explaining the gap.

## Directional confidence

`confidence_focal_to_partner` is the fraction of the *focal* entity's
own commits that also touched the partner, and
`confidence_partner_to_focal` is the reverse. The Jaccard ratios above
are symmetric and normalize by the union of both histories. The
directional numbers normalize by each side alone, so they can differ a
lot: a small file that almost always changes alongside a busy one will
show a high `confidence_focal_to_partner` and a low
`confidence_partner_to_focal`. Read them when the question is "if I
change X, how likely is Y to need a change too."

## After a rename

If the focal entity was renamed or refactored, its earlier identities'
co-change history is merged into the result so a real coupling doesn't
vanish at the rename. The response's `entity` block then carries
`lineage_prior_entity_ids`, the list of prior identities that were
folded in; the numbers can therefore differ from what you saw before the
rename, and that field is the visible reason. Whole-file renames are
detected from git's own content-similarity rename detection over the
most recent 2,000 commits, skipping commits that touch more than 50
files, so a `git mv` with edits is still followed.

## Two independent signals, deliberately separate vocabularies

`co_change_analysis` answers two different questions about each partner,
and it's worth keeping them apart explicitly — they used to share the
word "undeclared," which made a partner that was simultaneously "real"
on one axis and "already known about" on the other read as
self-contradictory. They're not related:

1. **Is there already a structural edge?** (`has_structural_dependency` /
   `is_undeclared_coupling`)
2. **Is the statistical git-history signal itself strong enough to
   trust?** (`classification`)

A partner can be `true` on structural dependency AND `strong_change_coupling`
on classification at the same time — that's two independently true facts,
not a contradiction.

## `has_structural_dependency` and `is_undeclared_coupling`

Every partner is checked against the graph for a direct `calls`,
`imports`, `inherits_from`, `implements`, `references_type`, or
`references_field_type` edge to the focal entity. When the focal entity
is a whole file, the check also looks for a `file_depends_on` edge: the
file's own symbol-level dependencies rolled up into one edge per target
file.

- `has_structural_dependency: true` — the coupling is at least partly
  explained by code you can already see (an import, a call). Expected,
  lower-priority signal.
- `is_undeclared_coupling: true` (equivalently, no structural
  dependency) — these two entities change together in git history with
  **nothing in the code connecting them**. This is the actually useful
  finding: a hidden dependency — shared assumptions, a config file and
  the code that reads it, a test fixture and what it exercises — that
  static analysis alone would never surface.

`summary.undeclared_couplings` gives you the count up front; check that
before scanning the full partner list.

## `classification`: telling real coupling apart from raw-count noise

A partner can clear the `co_change_min_commits` floor on raw count alone
while its `historical_strength` (Jaccard ratio) is still near zero — an
entity with a large individual change history can rack up a shared
commit or two with almost everything it's ever near, without any real
pattern. `classification` distinguishes the two: `strong_change_coupling`
(`historical_strength` at or above the floor — the real signal) versus
`weak_change_coupling` (below it — noise, even though it passed
the commit-count gate). The floor is `co_change_min_jaccard_strength`, `0.05`
by default. This is purely a statistical-confidence label —
it says nothing about whether a structural edge exists (see above); check
`has_structural_dependency`/`is_undeclared_coupling` for that. Weak
partners are excluded from the response by default; pass
`include_weak=true` to see them anyway, and
`_meta.excluded_weak_count`/`weak_hint` tell you how many were left out.
Pass `include_receipts=true` to get each partner's actual shared commits
(hash/author/timestamp, capped at 10) instead of taking the count on
faith — useful when a coupling looks surprising and you want to verify
it before acting on it. A partner whose entity ID no longer resolves in
the graph (renamed/deleted since the coupling was recorded) is excluded
from the response entirely rather than shown with null fields — see
`_meta.dangling_ref_count`/`dangling_ref_hint` if any were dropped.

## Before a refactor

Read the partner list ranked by `historical_strength`, focusing on the
undeclared ones first — those are the files most likely to need a
matching change that nothing in the dependency graph would warn you
about. `summary.strongest_coupling` is a shortcut to the single
strongest signal without scanning the whole list.

A partner with a low `co_change_count` (below the configured
`co_change_min_commits` floor, `2` by default) never appears at all —
this filters out one-off incidental co-edits (a mass reformat, an
unrelated commit that happened to touch two files) from ever masquerading
as a real coupling signal.

Three thresholds shape which pairings exist at all, in two places:

| Setting | Default | Applied | What it does |
|---|---|---|---|
| `co_change_min_commits` | `2` | when edges are written, and again at query time | Minimum shared commits for a pairing to exist. |
| `co_change_min_confidence` | `0.05` | when edges are written | Minimum directional confidence (shared commits as a fraction of an entity's own commits) for an edge to be written. Because it is relative to each entity's own history, it works the same on a small repo and a busy one; a fixed absolute commit count doesn't. |
| `co_change_min_jaccard_strength` | `0.05` | at query time | The `strong_change_coupling` / `weak_change_coupling` boundary on `historical_strength`. |

All three live in the `[graph]` section of the workspace config and can
be overridden with `CONTEXTUAL_CO_CHANGE_MIN_COMMITS`,
`CONTEXTUAL_CO_CHANGE_MIN_CONFIDENCE`, and
`CONTEXTUAL_CO_CHANGE_MIN_JACCARD_STRENGTH`. The first two take effect
on the next index; the last applies to the next query. The default of
`0.05` for the Jaccard floor was measured against real data from a large
public repository: generic build, CI, and lint files fell at or below
about `0.039`, and real source-to-source couplings at `0.069` and above.
That is evidence from one repository, not a universal constant, and a
narrow band between the two clusters can go either way (for example a
source file and its own test file).

## Why a narrow entity shows zero (or very few) partners

Entities spanning 3 lines or fewer — a short constant, a one-line
accessor — are excluded from cross-entity pairing entirely, even though
they still have their own commit history elsewhere in the system (via
`get_temporal_context`). The reason: a narrow entity with no history of
its own inherits its *containing file's* full commit history by design,
so a 3-line stub sitting in an actively-maintained file would otherwise
show up "coupled" to dozens of partners it has no real relationship
to — the file's coupling breadth, misattributed to a few lines of it.
If you're investigating a small entity and expected partners that
aren't showing up, this is why; look at the containing file's own
`co_change_analysis` result instead, which isn't subject to this
exclusion.

## See also

- `mcp/tools/reference/co_change_analysis` — parameters and when to
  reach for this tool over `graph_impact`.
- `nexus/explanation/nexus-signal-sources` — why `nexus_search` itself
  doesn't surface this signal, and reaches for this tool instead.
