# Changelog

All notable changes to Contextual are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Detailed release notes (with terminal examples) live under
[`docs/changelog/`](docs/changelog/); this file is the quick-scan version.

## [1.0.1] - 2026-09-07

### Added

- Indexing now runs as a background job with a fast keyword-searchable
  Dynamic Index tier ahead of full semantic embedding — `search` works
  before indexing finishes.
- Indexing jobs are crash-safe, resumable, and cancelable
  (`contextual index --cancel`).
- MCP Registry support — `uvx contextual-engine` now works alongside the
  `contextual` CLI.

### Changed

- `contextual doctor` reports indexing job health and index freshness.
- Dependency graph now records calls it couldn't resolve as
  `unresolved_call` triples instead of dropping them silently.
- `graph_impact` flags low-confidence results with a `speculative_callers`
  list and a `completeness` signal for `signature_change`.
- `graph_at_time` snaps to the nearest indexed commit instead of returning
  a bare `not_found`.
- Repeated graph lookups in the same session cost ~4x fewer tokens
  (`full_detail` / `no_preview` options).
- `get_telemetry` labels which data source each number comes from.
- Decision and history tools return a plain-language `relative_time`
  string alongside exact timestamps.

### Fixed

- Reduced false-positive noise in undeclared-coupling (`co_change`)
  results.
- New or updated architectural decisions could be invisible to search for
  up to the cache TTL.
- Inconsistent commit timestamps (author vs. committer time) across
  co-change and blame history.
- `entity_line_history` now respects `.git-blame-ignore-revs`.
- `graph_stats`' `density` field was mislabeled — split into
  `average_edges_per_entity` and `directed_density`.

[1.0.1]: https://pypi.org/project/contextual-engine/1.0.1/

## [1.0.0] - 2026-08-27

Launch day — `contextual-engine` is live on PyPI.

### Added

- Full temporal-first code memory engine: hybrid search, dependency-graph
  traversal, blast-radius impact analysis, and architectural decision
  history, shipped as an MCP server.
- 22 MCP tools spanning search, graph traversal, impact analysis,
  git-blame-enriched history, undeclared-coupling detection, and decision
  records.
- 14 languages supported out of the box (Python, JS/TS, Java, Go, Rust,
  C#, Kotlin, Swift, Ruby, PHP, and more).
- `Solo` plan launched for individual developers.

[1.0.0]: https://pypi.org/project/contextual-engine/1.0.0/
