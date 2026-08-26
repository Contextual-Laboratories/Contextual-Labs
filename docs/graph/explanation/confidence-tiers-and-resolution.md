---
title: Confidence tiers and resolution
domain: graph
category: explanation
tldr: "Every graph edge carries a confidence_tier — high, moderate, low, or speculative — so a query result tells you how certain a relationship is instead of asserting a flat yes/no."
order: 6
related:
  - graph/explanation/the-knowledge-graph.md
  - graph/how-to/interpret-a-blast-radius-report.md
  - graph/how-to/read-a-graph-traversal-result.md
---

<Callout variant="tldr">
Every edge in a graph tool's response carries a `confidence_tier`
label: `high`, `moderate`, `low`, or `speculative` (a fifth,
`unknown`, covers edges with no recorded score at all). Lower tiers are
excluded from most results by default and, where they are surfaced,
are labeled clearly rather than mixed in as if confirmed.
</Callout>

## Why edges aren't just true or false

Statically determining "what does this call actually resolve to" is
genuinely uncertain in most real codebases — the same method name can
exist on several unrelated classes, a call can resolve to any one of a
handful of subclass overrides at runtime, and a codebase's own naming
conventions vary in how much they help disambiguate. Rather than
guessing and asserting one confident answer, or refusing to answer at
all, every resolved edge carries a confidence score, and every graph
tool response surfaces that score as a human-readable tier rather than
a raw number a caller would otherwise have to interpret unassisted.

| Tier | Roughly | Meaning |
|---|---|---|
| `high` | ≥ 0.90 | Unambiguous — import-attested, or the only same-file/global candidate. |
| `moderate` | 0.75–0.89 | A confident but slightly less certain match — e.g. a unique name resolved without an explicit import. |
| `low` | 0.55–0.74 | An ambiguous fallback match — still included in default results, but the weakest tier that is. |
| `speculative` | < 0.55 | Below the default floor — `potential_call` edges live here (see below). |
| `unknown` | — | No confidence score was recorded for this edge at all. |

<Callout variant="note">
Treat these ranges as illustrative, not a stable numeric contract — the
tier *labels* (`high`/`moderate`/`low`/`speculative`/`unknown`) are what
every graph tool response actually exposes and what's meant to be
depended on; the exact boundaries behind them are an implementation
detail that has moved before and may again.
</Callout>

## What pushes an edge into a higher or lower tier

Two different resolution mechanisms feed into these tiers, for
different reasons:

**Name-based resolution** (most `calls`/`imports`/`inherits_from`
edges) leans on how unambiguous the match was: a call resolved via an
explicit import, or matching exactly one entity of that name anywhere
reachable from the caller, lands in the highest tier. A name that's
only unique if you ignore file/language boundaries, or that has more
than one plausible target, lands lower.

**CHA/RTA virtual-dispatch resolution** (`calls_polymorphic` edges,
see `graph/explanation/the-knowledge-graph`) tiers purely by how close
the override is to the call site, since that's a real, available signal
even when the exact runtime type can't be proven: an override
defined in the same file as the call is the strongest candidate; one
in a file the caller directly imports is next; one only reachable
through a longer import chain is weaker; and a same-name override with
no import relationship to the caller at all — a pure structural
guess — is the weakest tier that still gets recorded.

## The lowest tier is excluded by default, not deleted

A `potential_call` edge (the `speculative` tier) still gets written to
the graph — it isn't discarded — but ordinary `graph_traverse` and
`graph_impact` calls don't surface it unless the situation specifically
calls for it. The one deliberate exception: `graph_impact` with
`change_type="rename"` includes a separate, explicitly labeled
`speculative_callers` list precisely because an undercounted blast
radius before a rename is a worse failure mode than showing some
advisory-only, unconfirmed candidates alongside the confirmed ones. See
`graph/how-to/interpret-a-blast-radius-report` for how to read that
list without over-trusting it.

## What this means when you're reading a result

A response's `_meta._resolution_summary` (see
`graph/how-to/read-a-graph-traversal-result` and
`graph/how-to/interpret-a-blast-radius-report`) breaks down the
returned edges by tier. A result dominated by `high`/`moderate` edges
is close to a confirmed dependency graph; a result with a large
`speculative` count is telling you the codebase's naming or structure
made static resolution genuinely hard for that query, and the result
is a set of leads to verify, not a finished answer.

## See also

- `graph/explanation/the-knowledge-graph` — the resolution strategies
  (import-aware matching, cross-file inheritance, CHA/RTA, constructor
  type inference) that feed into these tiers.
- `graph/explanation/entity-and-predicate-taxonomy` — where
  `calls_polymorphic` and `potential_call` fit among every predicate.
