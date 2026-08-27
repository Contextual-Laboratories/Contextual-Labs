# Evaluation

How Contextual measures its own retrieval quality: the golden repos, the query suite, the metrics, and the hardware profiles behind any accuracy number we publish.

- `evaluation/explanation/why-these-golden-repos` — how the 15 pinned
  benchmark repos were selected: measured indexer-filtered file count
  against a target size band, one slot per language plus genuine
  polyglot pairings.
- `evaluation/explanation/how-we-evaluate-retrieval-quality` — the
  pipeline: 1,083 queries run through the real MCP tool path, scored
  on Recall@10/NDCG@10/MRR@10.
- `evaluation/explanation/what-a-benchmark-number-means-here` — why
  every result is tagged to a hardware profile and never compared
  across profiles.
- `evaluation/how-to/run-the-eval-suite-yourself` — real
  `run_eval.py`/`run_all.py` commands, flags, and a real example
  report.
- `evaluation/how-to/read-a-baseline-changelog-entry` — the changelog
  format and the exact regression tolerances.
- `evaluation/reference/golden-repo-list` — all 15 repos: language(s),
  license, pinned commit, indexed file count, LOC, on-disk size.
- `evaluation/reference/query-categories-and-tool-mapping` — the 11
  query categories and which MCP tool each one exercises.
- `evaluation/reference/current-benchmark-results` — live per-repo
  Recall@10/NDCG@10/MRR@10 and performance numbers.
- `evaluation/reference/how-the-decision-record-feature-is-verified` —
  the separate ADR-fixture suite that verifies chain integrity and
  search-supersession behavior.
