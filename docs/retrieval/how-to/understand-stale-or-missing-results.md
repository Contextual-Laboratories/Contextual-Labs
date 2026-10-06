---
title: Understand Why a Query Returned Stale or Missing Results
domain: retrieval
category: how-to
tldr: A missing or stale-looking result almost always traces to one of five causes — the index hasn't caught up with a recent change, Deep Index is still running in the background, the entity genuinely isn't in the graph, the top result fell below a real-match floor, or a high staleness score is correctly flagging content that's likely out of date.
order: 6
related:
  - indexing/explanation/incremental-vs-scheduled-indexing.md
  - temporal/explanation/temporal-intelligence.md
  - troubleshooting/entity-not-found.md
---

<Callout variant="tldr">
Before assuming something is broken, check which of these you're
actually looking at: the index hasn't caught up with a recent change
yet, the graph genuinely has no record of what you're asking about, the
top result didn't actually clear a real-match floor, or a result came
back with a high staleness score correctly telling you it's likely out
of date.
</Callout>

## Cause 1 — the index hasn't caught up yet

Contextual never reindexes on a timer — see
`indexing/explanation/incremental-vs-scheduled-indexing`. If you changed a
file very recently, and either the file watcher wasn't running when you
made the change or hasn't processed it yet, your query is running against
the old version of that file.

**Fix**: run `contextual index --incremental` and try again. This is
cheap and safe to run whenever you're unsure.

## Cause 2 — Deep Index is still running in the background

Right after a fresh or forced `contextual index`, the fast Dynamic Index
tier (graph, blame, keyword search) is queryable before Deep Index
(embeddings) finishes — see `indexing/explanation/how-indexing-works`.
During that window, `search` results come from keyword matching
(entity definitions first, then code-chunk matches, then whole files)
rather than full semantic ranking, and the response carries a
`DYNAMIC_INDEX_ONLY` entry in its stale warnings. `nexus_search` adds
that entry only when the query's own seed selection had to fall back to
keyword matching; a query answered from embeddings that already exist
doesn't get it. This isn't staleness in
the usual sense — it resolves on its own once Deep Index completes; check
`contextual index --status` to see how far along it is.

## Cause 3 — the entity genuinely isn't in the graph

For graph-specific tools (`graph_traverse`, `graph_impact`,
`graph_get_entity_callers`, and others), Contextual deliberately
distinguishes "this entity has zero results" from "this entity doesn't
exist in the index at all" — see
`troubleshooting/entity-not-found` for the full detail on this
distinction and why it matters. If you're seeing an explicit "not found"
message rather than an empty result, that's this cause, not staleness.

## Cause 4 — the result doesn't actually match, and Contextual is telling you so

`search`'s response can carry two different `stale_warnings` entries
that both mean "don't trust these results," for different reasons.
`WEAK_MATCH_SIGNAL` means nothing in the returned pool stood out from
the rest of it — a uniformly weak field. `NO_CONFIDENT_MATCH` catches
the opposite shape: a single result that *did* stand out from an
otherwise-weak pool (so `WEAK_MATCH_SIGNAL` wouldn't fire) while its own
embedding similarity still falls below a real-match floor — a generic,
highly-connected chunk that picks up partial term overlap with almost
any query. Either one appearing means this codebase likely doesn't have
strong matching vocabulary for that query; try different search terms
rather than trusting the top result's `confidence` field, which only
measures standout within this result set, not real-world relevance.

## Cause 5 — the result is stale, and Contextual is telling you so

Every entity the temporal layer touches carries a staleness score — how
likely it is to be out of date, based on how often it normally changes
and how long it's been quiet (see
`temporal/explanation/temporal-intelligence` for the full mechanism).
`nexus_search` results carry this score directly; a high value is
Contextual correctly flagging "double-check this before relying on it,"
not a bug. If a result reads as stale, that's often the system working
as intended rather than something to fix.

## A quick checklist

1. Did you (or your editor) actually save the change, and was the
   watcher/daemon running when you did? → run `contextual index
   --incremental`.
2. Does the response's stale warnings include `DYNAMIC_INDEX_ONLY`? →
   Deep Index is still running; check `contextual index --status`.
3. Is this a graph query returning an explicit "not found" rather than
   an empty result? → see `troubleshooting/entity-not-found`.
4. Does the response's `stale_warnings` include `WEAK_MATCH_SIGNAL` or
   `NO_CONFIDENT_MATCH`? → the top result didn't clear a real-match
   floor; try different search terms rather than trusting it.
5. Is the result present but flagged with high staleness? → that's a
   signal, not a failure — treat it the way you'd treat a stale-review
   nudge.
6. None of the above? Run `contextual doctor` and check the
   **Database**, **Daemon & Locks**, and **Indexing Job** lines — see
   `observability/how-to/interpreting-doctor-report`. An unhealthy index,
   a crashed indexing job, or a daemon serving a different workspace than
   you expect are the next most common causes.
