---
title: Config key index
domain: configuration
category: reference
tldr: "Every config.toml key, which subsystem it controls, and a link to that subsystem's own reference."
order: 2
related:
  - configuration/reference/configuration-reference.md
  - configuration/explanation/config-precedence.md
  - observability/reference/observability-configuration-reference.md
---

<Callout variant="tldr">
A flat lookup from key name to section, file, and the subsystem
reference that actually explains it — use
`configuration/reference/configuration-reference` for the full picture
with defaults; use this page when you already have a key name (from a
`config.toml` you're reading, or an error message) and want to know
what it does and where.
</Callout>

## Workspace config — `.contextual/config.toml`

| Key | Section | Subsystem reference |
|---|---|---|
| `embed_batch_size`, `write_batch_size`, `max_file_size_kb`, `workers` | `[indexing]` | `indexing/explanation/how-indexing-works` |
| `bm25_candidates`, `dense_candidates` | `[retrieval]` | `retrieval/explanation/retrieval-pipeline` |
| `rrf_k`, `rrf_dense_weight`, `rrf_bm25_weight`, `rrf_trigram_weight` | `[retrieval]` | `retrieval/explanation/hybrid-ranking-and-mmr-diversity` |
| `mmr_enabled`, `mmr_lambda`, `mmr_max_chunks_per_file`, `mmr_max_chunks_per_symbol` | `[retrieval]` | `retrieval/explanation/hybrid-ranking-and-mmr-diversity` |
| `hybrid_enabled`, `fallback_bm25_only` | `[retrieval]` | `retrieval/explanation/retrieval-pipeline` |
| `ef` | `[retrieval]` | `retrieval/reference/retrieval-configuration-reference` |
| `query_cache_ttl_seconds` | `[retrieval]` | `retrieval/reference/retrieval-configuration-reference` |
| `ann_index_row_threshold` | `[retrieval]` | `indexing/reference/sizing-and-machine-requirements` |
| `model_idle_evict_seconds`, `model_eviction_check_interval_seconds` | `[daemon]` | `mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload` |
| `co_change_min_commits`, `co_change_half_life_days` | `[graph]` | `temporal/how-to/interpret-co-change-analysis-results` |
| `staleness_decay_days` | `[graph]` | `temporal/explanation/staleness-scoring-and-bitemporal-fields` |

There is no `[cache]` section — see
`configuration/reference/configuration-reference` for why caching has
no user-tunable keys today. Chunk sizing and file-watcher debounce are
likewise fixed engine defaults, not `config.toml` keys.

## Global config — `~/.contextual/global_config.toml`

| Key | Section | Subsystem reference |
|---|---|---|
| `host`, `port` | `[daemon]` | `indexing/reference/platform-support` |
| `die_after_idle_hours`, `startup_timeout`, `workspace_idle_evict_minutes` | `[daemon]` | `mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload` |
| `rate_limit_per_minute`, `rate_limit_per_hour` | `[daemon]` | `mcp/server/explanation/mcp-client-access-control` |
| `compaction_on_startup`, `compaction_interval_hours`, `compaction_retention_hours` | `[daemon]` | `indexing/reference/sizing-and-machine-requirements` |
| `heap_monitor_enabled`, `heap_monitor_interval_seconds`, `heap_trim_threshold_mb`, `memory_trim_restart_threshold_count` | `[daemon]` | `indexing/reference/sizing-and-machine-requirements` |
| `log_level`, `log_format`, `log_to_file`, `log_file_path` | `[observability]` | `observability/explanation/how-tracing-and-logging-work` |
| `tracing_enabled`, `trace_sample_rate`, `service_name`, `service_version` | `[observability]` | `observability/explanation/how-tracing-and-logging-work` |
| `export_to_lancedb`, `export_batch_size`, `export_flush_interval_ms` | `[observability]` | `observability/how-to/enable-otel-export-for-a-debugging-session` |
| `retention_days`, `audit_log_retention_days`, `prune_on_startup`, `prune_interval_hours` | `[observability]` | `observability/reference/logs-and-retention-reference` |
| `instrument_indexing`, `instrument_retrieval`, `instrument_mcp`, `instrument_storage` | `[observability]` | `observability/explanation/how-tracing-and-logging-work` |
| per-client access policies | `[clients]` | `mcp/server/explanation/mcp-client-access-control` |

## See also

- `configuration/reference/configuration-reference` — full key list with defaults and descriptions.
- `configuration/explanation/config-precedence` — how the two files layer, and env var overrides.
