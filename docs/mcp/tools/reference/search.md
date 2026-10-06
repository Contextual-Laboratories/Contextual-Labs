---
title: "search"
domain: mcp-tools
category: reference
tldr: "search(query, intent, filters, limit, compact, workspace) — the default first call for any code/docs question, returning ranked chunks plus related graph nodes."
order: 1
---

<Callout variant="tldr">
`search` is the tool an AI client should reach for before answering any
question about this codebase's logic, structure, behavior, or history —
instead of guessing from training data. Returns ranked code/doc chunks,
related graph nodes, and freshness info.
</Callout>

## Parameters

- `query` (string, required) — natural-language question or code/symbol
  name.
- `intent` (`"code"` | `"docs"` | `"mixed"`, default `"mixed"`) —
  restrict to code chunks, doc chunks, or both.
- `filters` (object, optional) — filter by `language`, `file_path`
  (substring/prefix match), or `symbol_type`. Applied during retrieval,
  before the `limit` cut — see the note below.
- `limit` (integer, 1–100, default 10) — max ranked results.
- `compact` (boolean, default false) — return a pipe-separated compact
  text format instead of JSON.
- `workspace` (string, optional) — workspace name or path; omitted
  means auto-resolve.

## When to use it (and when not to)

Call it when a question references "the codebase," "our code," or a
symbol/file not already visible in context. Skip it if you already have
the exact file content from a prior `get_file_content` call, or the
question is about an unrelated codebase.

<Callout variant="note">
If Deep Index (embedding) hasn't finished yet for this workspace — right
after a fresh or forced `contextual index` — `search` automatically falls
back to Dynamic Index's keyword layer instead of returning incomplete
semantic results, and adds a `DYNAMIC_INDEX_ONLY` entry to the
response's stale warnings so you know results are keyword-matched, not
semantically ranked, for now. The keyword layer has three tiers, merged
in this order: entity-definition hits (a symbol whose definition matches
the query), then code-chunk matches (function- and class-sized pieces,
including module-level code and one piece of an oversized function), then
whole-file matches. Each file appears once, from the best tier that found
it. This switches back to the normal path the moment Deep Index
completes — see `indexing/explanation/how-indexing-works`.
</Callout>

<Terminal lines={[
  {command: "search(query=\"how does license validation work\", intent=\"code\")"},
  {output: "{\n  \"results\": [ ... 8 ranked chunks ... ],\n  \"related_nodes\": [ ... ],\n  \"latency_ms\": 84\n}", muted: true}
]} />

<Callout variant="note">
When `results` hits the `limit` cap, the response leads with a top-level
`_status` sentence ("Showing 10 results — more may exist — narrow your
query, add filters, or raise limit to see different results.") before
any other field — the same signal `truncated` already carried, just
easy to miss as a plain boolean. It nudges toward a narrower query
rather than pagination, since a follow-up "give me the rest" call is
rarely the right next step.
</Callout>

<Callout variant="note">
`truncated: true` can mean either of two different things: results hit
the `limit` cap (more matches may exist — narrow the query or raise
`limit`), or the response was cut short by a shared response-size
budget before `limit` was reached (check `response_budget.
excluded_by_budget`) — set `compact=True` for a denser encoding if
you're hitting this on a large `limit`.
</Callout>

<Callout variant="note">
Each result's code preview is a window of roughly 70 lines. For a chunk
longer than that, such as a whole-file fallback chunk that begins with
its imports or a merged chunk of several small siblings, the window is
centered on the first place a word from your query appears rather than
always starting at the top of the chunk, so the preview shows the part
that matched.
</Callout>

<Callout variant="note">
Documentation chunks are ranked below code chunks of equal relevance, so
a prose file doesn't outrank the code it describes. A document whose
filename contains `audit` or `review` (a report about the codebase, for
example) gets an additional, steeper discount, because such documents
tend to repeat a query's own vocabulary without being the answer. READMEs
and ordinary architecture docs aren't affected by that second discount.
</Callout>

<Callout variant="note">
A bad `filters.language` or `filters.symbol_type` value (a typo, or the
wrong case) now raises an explicit error listing the allowed values,
instead of silently returning an empty `results` list that looked
identical to a genuine no-match. `filters.file_path` has no fixed
vocabulary to validate against, so a `file_path` that matches nothing
is still a legitimate empty result, not an error.
</Callout>

<Callout variant="note">
`filters` are applied **during retrieval**, before results are cut to
`limit`: the filter becomes part of each candidate search (keyword, vector,
symbol-name and exact-symbol), so you get the best matches *within* the
filter, not whatever survived being ranked first and filtered afterwards.
`file_path` is a substring match (an exact path, a directory prefix, or a
bare filename all work); `%` and `_` in a path are literal. A `language`
filter excludes documentation chunks, which have no language.

One exception: while the deep index is still building (a
`DYNAMIC_INDEX_ONLY` warning), search runs a keyword-only fallback where
filters can only be applied after ranking. There, if they remove results
from a full pool, `stale_warnings` carries `FILTERED_AFTER_RANKING` with
the real numbers, and an empty `results` list is flagged as *not* proof
that nothing matches — raise `limit` (up to 100) to reach hidden matches.
</Callout>

<Callout variant="note">
Two distinct `stale_warnings` entries mean "don't trust these results,"
for different reasons: `WEAK_MATCH_SIGNAL` means nothing in the result
set stood out from the rest of it (a weak field, not a confident
match). `NO_CONFIDENT_MATCH` means the opposite kind of gap — the top
result *did* stand out from an otherwise-weak pool, but its own
embedding similarity still fell below a real-match floor. Either one
showing up is a signal to try different search terms, not to trust the
top result's `confidence` field, which only measures relative
standout within this result set.
</Callout>

## See also

- `mcp/tools/reference/nexus_search` — the richer, graph-enriched
  version of this same idea.
- `mcp/tools/how-to/choose-between-search-and-nexus-search`.
