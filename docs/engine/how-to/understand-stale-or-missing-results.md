---
title: Understand Why a Query Returned Stale or Missing Results
domain: engine
category: how-to
tldr: A missing or stale-looking result almost always traces to one of three causes — the index hasn't caught up with a recent change, the entity genuinely isn't in the graph, or a high staleness score is correctly flagging content that's likely out of date.
order: 6
related:
  - engine/explanation/incremental-vs-scheduled-indexing.md
  - engine/explanation/temporal-intelligence.md
  - engine/troubleshooting/entity-not-found.md
---

<Callout variant="tldr">
Before assuming something is broken, check which of three things you're
actually looking at: the index hasn't caught up with a recent change yet,
the graph genuinely has no record of what you're asking about, or a
result came back with a high staleness score correctly telling you it's
likely out of date.
</Callout>

## Cause 1 — the index hasn't caught up yet

Contextual never reindexes on a timer — see
`engine/explanation/incremental-vs-scheduled-indexing`. If you changed a
file very recently, and either the file watcher wasn't running when you
made the change or hasn't processed it yet, your query is running against
the old version of that file.

**Fix**: run `contextual index --incremental` and try again. This is
cheap and safe to run whenever you're unsure.

## Cause 2 — the entity genuinely isn't in the graph

For graph-specific tools (`graph_traverse`, `graph_impact`,
`graph_get_entity_callers`, and others), Contextual deliberately
distinguishes "this entity has zero results" from "this entity doesn't
exist in the index at all" — see
`engine/troubleshooting/entity-not-found` for the full detail on this
distinction and why it matters. If you're seeing an explicit "not found"
message rather than an empty result, that's this cause, not staleness.

## Cause 3 — the result is stale, and Contextual is telling you so

Every entity the temporal layer touches carries a staleness score — how
likely it is to be out of date, based on how often it normally changes
and how long it's been quiet (see
`engine/explanation/temporal-intelligence` for the full mechanism).
`nexus_search` results carry this score directly; a high value is
Contextual correctly flagging "double-check this before relying on it,"
not a bug. If a result reads as stale, that's often the system working
as intended rather than something to fix.

## A quick checklist

1. Did you (or your editor) actually save the change, and was the
   watcher/daemon running when you did? → run `contextual index
   --incremental`.
2. Is this a graph query returning an explicit "not found" rather than
   an empty result? → see `engine/troubleshooting/entity-not-found`.
3. Is the result present but flagged with high staleness? → that's a
   signal, not a failure — treat it the way you'd treat a stale-review
   nudge.
4. None of the above? Run `contextual doctor` and check the
   **Database** and **Daemon & Locks** lines — see
   `engine/observability/interpreting-doctor-report`. An unhealthy index
   or a daemon serving a different workspace than you expect are the
   next most common causes.
