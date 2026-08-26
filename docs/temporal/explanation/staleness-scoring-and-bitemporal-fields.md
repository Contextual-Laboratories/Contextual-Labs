---
title: Staleness scoring and bitemporal fields
domain: temporal
category: explanation
tldr: "How the blame pipeline scores result staleness and what the bitemporal fields track."
order: 6
related:
  - temporal/explanation/temporal-intelligence.md
  - graph/reference/graph-schema-reference.md
  - nexus/explanation/nexus-enrichment.md
---

<Callout variant="tldr">
Staleness combines an entity's rolling change velocity with how long
it's been quiet since, decaying exponentially rather than stepping at a
fixed age — a high-velocity entity gone quiet reads as more suspicious
than a low-velocity one that's always been quiet. Every graph entity
and edge also carries a real bitemporal range: when a version was true
about your code, separately from when Contextual recorded it.
</Callout>

## The staleness formula

```
staleness = change_velocity × (1 − e^(−days_since / k))
```

- `change_velocity` — changes per day over a rolling 30-day window.
  `0` for an entity that's never changed recently, in which case
  staleness is always `0.0` regardless of age — an inherently stable
  entity going quiet isn't suspicious, it's expected.
- `days_since` — days since this entity's current version became
  valid (its `valid_at`).
- `k` — the decay constant, `30` days by default, controlling how fast
  the `(1 − e^(−days_since/k))` term approaches `1`. Configurable via
  `staleness_decay_days` under `[graph]` in `config.toml`, or the
  `CONTEXTUAL_STALENESS_DECAY_DAYS` environment variable.

The score is clamped to `[0.0, 1.0]` and recomputed at retrieval time
(inside `nexus_search`) as well as at index time, so a node you get
back reflects its staleness as of right now, not a value computed once
during indexing and never refreshed.

### Score bands

| Score | Label | Meaning |
|---|---|---|
| `< 0.4` | `fresh` | Stable, or genuinely recently touched. |
| `0.4 – 0.7` | `aging` | Worth noting, not yet a concern. |
| `0.7 – 0.9` | `stale` | Sorts to the end of `nexus_search` results; `is_stale` becomes `true` at this threshold. |
| `≥ 0.9` | `critical` | High enough to be worth a direct warning before trusting this entity's current state. |

<Callout variant="note">
Staleness is a heuristic worth double-checking, not a correctness
signal — a fresh score doesn't guarantee an entity is accurate, and a
critical one doesn't prove it's wrong. It's the same kind of "look
here first" nudge a code reviewer would give, not a validation gate.
</Callout>

## What the bitemporal fields actually track

Every row in the `entities` and `triples` tables (see
`graph/reference/graph-schema-reference` for the full column list)
carries two independent time ranges, not one "last updated"
timestamp:

- **Valid time** (`valid_at` / `invalid_at`) — when this version was
  *true about your code*. A function's valid-time range starts at the
  commit that introduced its current body and ends (via `invalid_at`)
  the moment a later commit changes it — or stays open (`NULL`) if
  it's still the current version.
- **Transaction time** (`tx_at` / `expired_at`) — when Contextual
  *recorded* that fact. This can lag valid time — a re-index run
  processes a batch of historical commits and writes rows whose valid
  time is in the past, all with a transaction time of "just now."

Keeping these separate is what lets `graph_at_time` answer "what did
the graph look like as of commit X" by filtering on valid time, while
still being able to distinguish that from "what did Contextual believe
at that point" if the two ever diverge (e.g. a backfill correcting a
previously-wrong resolution). A single entity or edge can have many
historical versions stacked over time, each with its own closed valid-time
range except the current one, which stays open.

## See also

- `temporal/explanation/temporal-intelligence` — the blame pipeline and
  mailmap resolution these scores and fields build on.
- `graph/reference/graph-schema-reference` — the exact table columns.
- `mcp/tools/reference/graph_at_time` — the tool that reads valid time
  to reconstruct a past snapshot.
