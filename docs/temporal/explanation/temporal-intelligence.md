---
title: Temporal Intelligence
domain: temporal
category: explanation
tldr: Contextual attaches real git blame, commit history, and a decaying staleness score to graph entities, and can answer "what did the graph look like at commit X" via bitemporal snapshots — not just "what does the code look like now."
order: 5
related:
  - graph/explanation/the-knowledge-graph.md
  - indexing/explanation/how-indexing-works.md
  - mcp/tools/reference/graph_at_time.md
---

<Callout variant="tldr">
Every graph entity carries real git blame and commit attribution, not
just current-state code. A staleness score estimates how likely an
entity is to be out of date given how often it changes and how long it's
been quiet, and `graph_at_time` can reconstruct what the graph looked
like as of a past commit or timestamp.
</Callout>

## Blame is a real subprocess pipeline, not a guess

Attribution comes from actually running `git blame` (first-parent,
whitespace-ignoring) as a native subprocess per file, with an enforced
timeout, and caching the result. This is deliberately not an in-process
git library call — an earlier in-process approach was tried and reverted
after it caused a multi-hour hang on a large real-world repository, with
no way to kill a stuck native thread from the timeout logic. A subprocess
can be killed outright if it overruns; that reliability trade was judged
worth more than the extra process-spawn overhead.

`.git-blame-ignore-revs` is auto-detected at your repo root and honored —
if you use it to exclude a mass-reformat commit from blame attribution,
Contextual respects that the same way `git blame --ignore-revs-file`
does natively, and invalidates its own cache automatically when that
file's contents change.

## Every commit walk is mailmap-aware

If your repository uses a `.mailmap` file to consolidate an author's
multiple emails/names into one canonical identity, both the native git
blame path and Contextual's own incremental git layer resolve through it
— so `co_change_analysis` and authorship data reflect one real person,
not fragmented aliases.

## Staleness is a decaying function of change frequency, not a fixed age cutoff

An entity's staleness score combines two things: how often it tends to
change (its rolling change velocity) and how long it's been quiet since.
A file that changes constantly and has gone quiet for a while scores as
more "worth double-checking" than a file that's always been quiet — the
same raw number of days-since-last-change means something different
depending on the entity's normal rhythm. The score decays exponentially
with time rather than stepping at a fixed threshold, and crosses four
named bands from "fresh" through "worth noting" to "critical enough to
surface a warning to the AI client directly." This score is computed
both when content is indexed and again at retrieval time inside
`nexus_search`, so a node returned by nexus enrichment already carries
its current staleness rather than a value computed once and never
refreshed.

<Callout variant="note">
Staleness is a heuristic signal for "this might be worth a closer look,"
not a correctness guarantee — a low staleness score doesn't prove an
entity is accurate, and a high one doesn't prove it's wrong. Treat it the
way you'd treat a code-review nudge, not a validation check.
</Callout>

## Bitemporal fields let you ask "as of when"

Graph entities and relationships both carry a set of temporal fields
tracking when a version became valid, when it stopped being valid, and
when it was actually written to the database — a real bitemporal model,
not just a single "last updated" timestamp. This is what backs
`graph_at_time`: give it a past commit SHA or an ISO timestamp, and it
reconstructs the graph as it existed at that point by filtering on these
fields, rather than only ever being able to answer questions about the
current state of your code.

`graph_at_time` snapshots are restricted to structural relationships at a
meaningful confidence floor — the same lowest-confidence tier excluded
from live graph queries by default is also excluded here, and
git-history-derived edges (like co-change relationships) are excluded
from the traversal itself, since "who co-changed with this file as of
six months ago" isn't the kind of structural snapshot this tool is
answering.

## Where this shows up

Per-entity commit history (`get_temporal_context`), the "who last
touched this and why" side of a `nexus_search` result, and
`co_change_analysis`'s file-coupling detection (see
`nexus/explanation/nexus-enrichment`) are all built on the same
temporal foundation described here.
