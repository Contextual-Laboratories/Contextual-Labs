---
title: Configuration Reference
domain: configuration
category: reference
tldr: Two config files — a per-workspace config.toml for indexing/retrieval/graph tuning, and a global config.toml for daemon process and client-access settings — both TOML, both editable via contextual config.
order: 1
related:
  - indexing/reference/sizing-and-machine-requirements.md
  - mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload.md
  - cli/reference/config/config.md
---

<Callout variant="tldr">
There are two separate config files: your workspace's `.contextual/
config.toml` (indexing, retrieval, graph tuning — one per
workspace) and `~/.contextual/global_config.toml` (daemon process
settings and per-client MCP access policies — one per machine). Both are
plain TOML, both are opened for editing with `contextual config`
(`--global` for the second one).
</Callout>

## Workspace config — `.contextual/config.toml`

Created by `contextual init`, one per workspace, read fresh at daemon
startup (see `mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload` for
why a config edit needs `contextual mcp restart` to take effect).

### `[indexing]`

| Key | Default | What it controls |
|---|---|---|
| `embed_batch_size` | 32 | Chunks per embedding call — larger means fewer database round-trips per index run. |
| `write_batch_size` | 250 | Chunks per LanceDB write batch. |
| `max_file_size_kb` | 500 | Files larger than this fall back to line-based chunking instead of full tree-sitter parsing. |
| `workers` | 8 | Async concurrency for indexing tasks. |

Chunk sizing itself (target/max chunk bytes — see
`indexing/explanation/how-indexing-works`) is a fixed engine constant today,
not a `config.toml`-exposed setting. The file-watcher's debounce window is
likewise a fixed internal default, not a live config key.

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

### `[daemon]`, `[graph]`

There is no `[cache]` section — caching has no user-tunable knobs today.
In-process query-result cache capacity is a fixed engine default; its TTL is
actually the `[retrieval].query_cache_ttl_seconds` key above, not a separate
cache setting; and the embedding cache has no TTL or size cap at all — it's
reclaimed only by the global config's compaction schedule below.

The workspace-level `[daemon]` section
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
| `rate_limit_per_minute` / `rate_limit_per_hour` | 600 / 12,000 | Flat, per-client MCP request caps — same limit for every client regardless of access level. |
| `compaction_on_startup` / `compaction_interval_hours` / `compaction_retention_hours` | true / 6.0 / 1.0 | LanceDB fragment/version compaction schedule — keeps high-churn tables from accumulating unbounded version files. |
| `heap_monitor_enabled` / `heap_monitor_interval_seconds` / `heap_trim_threshold_mb` | true / 30 / 800 | Memory-trim behavior — see `indexing/reference/sizing-and-machine-requirements`. |
| `memory_trim_restart_threshold_count` | 5 | Consecutive still-over-threshold trims (with no active sessions) before the daemon requests its own clean restart instead of retrying a trim that isn't reclaiming anything. `0` disables the restart backstop. |
| `workspace_idle_evict_minutes` | 30.0 | Minutes a workspace can go untouched before its file watcher, connector, and caches are released (transparently re-initialized on the next tool call). `0` disables. |

Daemon process log verbosity is `[observability].log_level`, not a
`[daemon]` key — see below.

The `[observability]` section of this same file holds logging, tracing,
export, and retention settings — see
`observability/reference/observability-configuration-reference` rather
than this page for the full field list, since it's substantial enough
to warrant its own reference.

The `[clients]` section of this same file holds per-client MCP access
policies — see `mcp/server/explanation/mcp-client-access-control` rather than
this page for that mechanism, since it's a distinct system from tuning
knobs.

## Managing config

`contextual config [--global]` opens the relevant file in your editor,
creating it with defaults first if it doesn't exist yet. `contextual
config reset [--global]` restores defaults after a confirmation prompt —
see `cli/reference/config/config.md` and `cli/reference/config/config-reset.md`. A
workspace reset never re-mints the workspace ID, so your existing indexed
data stays associated with it.

<Callout variant="warning">
Any edit here requires `contextual mcp restart` to take effect — both
config files are read once at daemon startup, not on a live reload. See
`mcp/server/how-to/restart-the-daemon-after-a-code-or-config-change`.
</Callout>
