---
title: Configuration Reference
domain: configuration
category: reference
tldr: Two config files — a per-workspace config.toml for indexing/retrieval/cache tuning, and a global config.toml for daemon process and client-access settings — both TOML, both editable via contextual config.
order: 1
related:
  - indexing/reference/sizing-and-machine-requirements.md
  - mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload.md
  - cli/reference/config/config.md
---

<Callout variant="tldr">
There are two separate config files: your workspace's `.contextual/
config.toml` (indexing, retrieval, cache, graph tuning — one per
workspace) and `~/.contextual/global_config.toml` (daemon process
settings and per-client MCP access policies — one per machine). Both are
plain TOML, both are opened for editing with `contextual config`
(`--global` for the second one).
</Callout>

<Callout variant="note">
Earlier, a freshly-scaffolded workspace `config.toml`'s `[retrieval]`
section used a different (nested) key shape than the live retrieval
pipeline actually read — a documented config key that silently did
nothing is worse than an undocumented one. This has been fixed: the
scaffolder and the live retrieval pipeline now agree on the same flat key
shape, verified with round-trip tests. Everything below reflects the
current, correct shape.
</Callout>

## Workspace config — `.contextual/config.toml`

Created by `contextual init`, one per workspace, read fresh at daemon
startup (see `mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload` for
why a config edit needs `contextual mcp restart` to take effect).

### `[indexing]`

| Key | Default | What it controls |
|---|---|---|
| `embed_batch_size` | 128 | Chunks per embedding call — larger means fewer database round-trips per index run. |
| `write_batch_size` | 250 | Chunks per LanceDB write batch. |
| `chunk_soft_limit` / `chunk_hard_limit` | 1500 / 2000 | Target and max chunk size in bytes (see `indexing/explanation/how-indexing-works`). |
| `chunk_overlap` | 50 | Byte overlap between adjacent chunks. |
| `max_file_size_kb` | 500 | Files larger than this fall back to line-based chunking instead of full tree-sitter parsing. |
| `workers` | 8 | Async concurrency for indexing tasks. |
| `debounce_ms` | 500 | File-watcher debounce window (see `indexing/explanation/incremental-vs-scheduled-indexing`). |

### `[retrieval]`

Flat keys, matching the live `search` ranking pipeline exactly (see
`retrieval/explanation/retrieval-pipeline`):

| Key | Default | What it controls |
|---|---|---|
| `bm25_candidates` / `dense_candidates` | 100 / 100 | Candidates pulled from each retrieval method before fusion. |
| `rrf_k` | 60 | RRF's rank-damping constant. |
| `rrf_dense_weight` / `rrf_bm25_weight` / `rrf_trigram_weight` | 0.50 / 0.35 / 0.15 | Per-method weight in fusion. |
| `mmr_enabled` | true | Whether the diversity pass runs at all. |
| `mmr_lambda` | 0.7 | Relevance vs. diversity balance — higher favors relevance. |
| `mmr_max_chunks_per_file` / `mmr_max_chunks_per_symbol` | 2 / 1 | Diversity quotas. |
| `hybrid_enabled` | true | Whether dense+trigram run at all, vs. BM25-only. |
| `fallback_bm25_only` | true | Whether to silently degrade to BM25-only if hybrid search can't run, rather than failing the query. |
| `ef` | 0 (LanceDB default) | HNSW query-time candidate-list size for the vector index. |
| `query_cache_ttl_seconds` | 300 | How long an identical query's result is cached. |
| `ann_index_row_threshold` | 50,000 | Row count at which background ANN index maintenance kicks in below flat-scan. |

### `[cache]`, `[daemon]`, `[graph]`

`[cache]` controls the embedding cache (`enabled`, `max_size_mb` default
512, `ttl_seconds` default 3600). The workspace-level `[daemon]` section
is narrower than it sounds — it only controls embedding-model memory
eviction (`model_idle_evict_seconds` default 1800, i.e. 30 minutes of
inactivity before the ONNX arena is released;
`model_eviction_check_interval_seconds` default 300), overridable via
`CONTEXTUAL_MODEL_IDLE_EVICT_SECONDS`/`CONTEXTUAL_MODEL_EVICTION_CHECK_INTERVAL`.
`[graph]` controls co-change edge sensitivity (`co_change_min_commits`
default 2, `co_change_half_life_days` default 180) and staleness decay
(`staleness_decay_days` default 30), overridable via
`CONTEXTUAL_CO_CHANGE_MIN_COMMITS`, `CONTEXTUAL_CO_CHANGE_HALF_LIFE_DAYS`,
and `CONTEXTUAL_STALENESS_DECAY_DAYS` respectively.

## Global config — `~/.contextual/global_config.toml`

One per machine, not per workspace. This is where actual daemon-process
settings live — a different, and larger, `[daemon]` section than the
workspace-level one above.

| Key | Default | What it controls |
|---|---|---|
| `host` / `port` | `127.0.0.1` / `9091` | Only meaningful on Windows — macOS/Linux serve over a Unix domain socket instead (see `indexing/reference/platform-support`). |
| `die_after_idle_hours` | 2.0 | Hours of no client activity or indexing before the daemon exits cleanly on its own. `0` disables this. |
| `startup_timeout` | 15 (seconds) | How long daemon startup is given to report ready before it's treated as a failure. |
| `rate_limit_per_minute` / `rate_limit_per_hour` | 120 / 2000 | Flat, per-client MCP request caps — same limit for every client regardless of access level. |
| `compaction_on_startup` / `compaction_interval_hours` / `compaction_retention_hours` | true / 6.0 / 1.0 | LanceDB fragment/version compaction schedule — keeps high-churn tables from accumulating unbounded version files. |
| `heap_monitor_enabled` / `heap_monitor_interval_seconds` / `heap_trim_threshold_mb` | true / 30 / 800 | Memory-trim behavior — see `indexing/reference/sizing-and-machine-requirements`. |
| `log_level` | `INFO` | Daemon process log verbosity. |

The `[clients]` section of this same file holds per-client MCP access
policies — see `mcp/server/explanation/mcp-client-access-control` rather than
this page for that mechanism, since it's a distinct system from tuning
knobs.

## Managing config

`contextual config [--global]` opens the relevant file in your editor,
creating it with defaults first if it doesn't exist yet. `contextual
config reset [--global]` restores defaults after a confirmation prompt —
see `cli/reference/config/config.md` and `cli/reference/config/config/config-reset.md`. A
workspace reset never re-mints the workspace ID, so your existing indexed
data stays associated with it.

<Callout variant="warning">
Any edit here requires `contextual mcp restart` to take effect — both
config files are read once at daemon startup, not on a live reload. See
`mcp/server/how-to/restart-the-daemon-after-a-code-or-config-change`.
</Callout>
