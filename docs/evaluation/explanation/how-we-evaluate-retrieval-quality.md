---
title: How We Evaluate Retrieval Quality
domain: evaluation
category: explanation
tldr: 1,083 hand-authored queries with known-correct expected files, run through the real MCP tool path against 15 pinned production repos, scored on Recall@10, NDCG@10, and MRR@10.
related:
  - evaluation/reference/query-categories-and-tool-mapping.md
  - evaluation/reference/golden-repo-list.md
  - evaluation/explanation/what-a-benchmark-number-means-here.md
---

<Callout variant="tldr">
1,083 hand-authored queries, each with a known-correct set of expected
files, run against 15 pinned repos through the exact same MCP tool
path a real AI client uses — then scored on Recall@10, NDCG@10, and
MRR@10, broken down by both query category and repo.
</Callout>

## The real path, not a shortcut

An eval run doesn't call into Contextual's retrieval internals
directly. It indexes the target repo with the real `contextual` CLI,
spawns the real `contextual-mcp` stdio shim, and speaks real MCP
JSON-RPC to it — the same process an AI client like Claude Code,
Cursor, or Windsurf would go through. A query's tool call, response
shape, and latency are all exactly what an end user would actually
get, not a synthetic approximation of it.

## The query set

1,083 queries live in `eval/queries.yml`, spread across all 15 golden
repos and 11 categories — see
`evaluation/reference/query-categories-and-tool-mapping` for the full
breakdown and which MCP tool each category exercises. Every query
carries a hand-written `expected.files` list: the file(s) a correct
answer should surface, established by mining and verifying against the
repo's own live indexed graph rather than guessed — an `expected`
entry that can't be confirmed against real data doesn't go in.

## What counts as a hit

A query is a **hit** if any file in its `expected.files` list appears
anywhere in the top 10 results actually returned by the tool call —
matched on exact path, path suffix, or substring, to tolerate
reasonable path-representation differences between what a tool returns
and how the expected path was written. Rank position beyond "in the
top 10" only matters for NDCG and MRR (below), not for the hit/miss
decision itself.

## The three metrics

All three are computed at a fixed cutoff of 10 — no other cutoff is
used anywhere in the suite:

- **Recall@10** — the fraction of queries with at least one hit in the
  top 10. The headline number, and the one that's hard-gated against
  regression.
- **NDCG@10** — rewards a hit ranking higher, not just being present:
  `1 / log2(rank + 1)` for a hit at a given rank, averaged across all
  queries (0 for a miss).
- **MRR@10** — the mean reciprocal rank of the hit (`1 / rank`,
  0 for a miss) — similar in spirit to NDCG but penalizes a low rank
  more steeply.

Both NDCG@10 and MRR@10 are computed against a single known-correct
answer per query, not multi-grade relevance judgments — a simplified
form suited to "is the right file findable, and how quickly" rather
than a full ranked-relevance benchmark.

## Two more breakdowns, not just one headline number

Every run's metrics are also broken down **by category** and **by
repo**, not just reported as one blended figure. This is what actually
makes a result useful for engineering — a repo-wide Recall@10 of 90%
can still be hiding a category (co-change analysis, say, or temporal
queries) that's meaningfully weaker than the rest, and that only shows
up once the breakdown is looked at directly rather than trusting the
headline number alone.

## What else gets measured alongside accuracy

Every run also records median query latency, response size (median
and p95 token count), indexing duration, and peak RSS during indexing.
These aren't retrieval-quality metrics, but they're tracked in the
same baseline entry — see
`evaluation/explanation/what-a-benchmark-number-means-here` for how
they factor into a run's pass/fail result differently than the three
accuracy metrics do.
