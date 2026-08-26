# Retrieval

How a query becomes ranked results — hybrid BM25 + dense + trigram scoring and MMR diversity.

- `retrieval/explanation/retrieval-pipeline` — the full pipeline: three
  methods, RRF fusion, the exact-match override, no reranker.
- `retrieval/explanation/hybrid-ranking-and-mmr-diversity` — the exact
  fusion and MMR formulas, including the structural near-duplicate penalty.
- `retrieval/reference/retrieval-configuration-reference` — every
  `[retrieval]` config.toml field and its default.
- `retrieval/how-to/tune-retrieval-quality-for-your-codebase` — practical
  adjustments, and the daemon-restart step people forget.
- `retrieval/how-to/understand-stale-or-missing-results` — the three
  real causes of "why didn't I get the result I expected."
