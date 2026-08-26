# Temporal intelligence

Blame-enriched history, staleness scoring, bitemporal snapshots, and
co-change analysis — how Contextual attaches "when" and "who" to every
graph entity, not just "what."

- `temporal/explanation/temporal-intelligence` — the real git-blame
  pipeline, mailmap-aware attribution, and how bitemporal snapshots
  back `graph_at_time`.
- `temporal/explanation/staleness-scoring-and-bitemporal-fields` — the
  exact staleness formula and score bands, and what the `valid_at`/
  `invalid_at`/`tx_at`/`expired_at` fields track in practice.
- `temporal/how-to/interpret-co-change-analysis-results` — reading
  `co_change_analysis` output before a refactor: coupling strength,
  declared vs. undeclared coupling.
- `temporal/reference/temporal-query-reference` — exactly how
  `get_temporal_context` and `graph_at_time` map their inputs onto real
  git history.
