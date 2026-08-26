---
title: Tune retrieval quality for your codebase
domain: retrieval
category: how-to
tldr: "Practical adjustments when default retrieval settings underperform on your repo's shape — and the one step people forget: restart the daemon after editing config.toml."
order: 5
related:
  - retrieval/reference/retrieval-configuration-reference.md
  - retrieval/explanation/hybrid-ranking-and-mmr-diversity.md
---

<Callout variant="tldr">
Every setting here lives in `[retrieval]` in `config.toml`
(`contextual config`). After editing it, run `contextual mcp restart` —
the daemon reads this file once per workspace, the first time it
connects, and caches it; it will not notice an edit on its own.
</Callout>

<Callout variant="warning">
This is the single most common reason a config change appears to do
nothing: the daemon builds and caches a workspace's retrieval pipeline
(including its `[retrieval]` settings) the first time it connects to
that workspace, not on every query. Editing `config.toml` while the
daemon is already running against that workspace has no effect until
you run `contextual mcp restart`.
</Callout>

## Results feel repetitive — same file or near-duplicate chunks dominate

Two different mechanisms can cause this, and they're worth telling
apart before you start changing numbers:

- If the same **file** or **symbol** keeps showing up more than you'd
  expect, check `mmr_max_chunks_per_file` / `mmr_max_chunks_per_symbol`
  first — these are hard caps, and Contextual's defaults are
  deliberately tight (2 and 1). Raising them (rather than lowering
  `mmr_lambda`) is the more targeted fix if the actual complaint is
  "too many hits from one place," not "results feel too similar to
  each other generally."
- If results from *different* files/symbols still feel like near-copies
  of each other, that's the diversity/relevance tradeoff itself —
  lower `mmr_lambda` (toward `0.0`) to favor diversity more, or raise
  it (toward `1.0`) if you'd rather have Contextual take generally
  looser diversity and trust relevance more. See
  `retrieval/explanation/hybrid-ranking-and-mmr-diversity` for exactly
  what this trades off.

## Search misses an identifier you know is in the codebase

If a query using an exact or near-exact identifier isn't surfacing a
result you know exists, and it isn't a single bare identifier (which
gets an automatic exact-match override — see
`retrieval/explanation/retrieval-pipeline`), the underlying candidate
pool may simply be too narrow for a large or unusually diverse repo.
Raise `bm25_candidates` and `dense_candidates` (and, if needed,
`rrf_candidates` to match) to widen how much each method contributes
before fusion narrows things down. This costs more per-query work, so
raise it incrementally rather than jumping straight to a large number.

## Your codebase leans heavily on unusual naming conventions

Dense (embedding) search is weighted most heavily by default because
it's usually the strongest single signal for natural-language queries.
A codebase with heavy abbreviation, generated identifiers, or naming
conventions that don't read as natural language (a lot of `snake_case`
mixed with domain-specific shorthand, for example) may get more value
from BM25 or trigram matching than the default weighting assumes.
Raising `rrf_bm25_weight` or `rrf_trigram_weight` relative to
`rrf_dense_weight` shifts fusion toward exact/fuzzy identifier matching
without disabling semantic search entirely. There's no single right
ratio here — this is a "try it and see" adjustment specific to your
codebase's naming style, not a setting with a universally better value.

## Hybrid search seems to have silently turned off

If results feel like plain keyword search — no semantic matches for
conceptual queries that don't share exact wording — check two things
before assuming your config is wrong:

1. Is `hybrid_enabled` actually `true`? If someone disabled it
   deliberately (or `fallback_bm25_only` kicked in because the vector
   index wasn't ready), you're getting BM25-only results by design, not
   by accident.
2. Has the workspace actually finished a semantic index pass? Hybrid
   search depends on a populated vector index — see
   `cli/reference/general/doctor`'s Database check, and
   `indexing/reference/sizing-and-machine-requirements` for what a
   first index run involves.

## After any change

```
contextual mcp restart
```

Then re-run the query you were tuning against. If you're evaluating a
change's effect systematically rather than by feel, `contextual
activity` (see `cli/reference/general/activity`) can help you compare
call volume and latency before/after, though it doesn't score result
quality directly — that judgment is still yours.

## See also

- `retrieval/reference/retrieval-configuration-reference` — every
  field's exact default.
- `retrieval/explanation/hybrid-ranking-and-mmr-diversity` — the
  mechanics each of these settings actually feeds into.
- `cli/reference/mcp/mcp-restart`.
