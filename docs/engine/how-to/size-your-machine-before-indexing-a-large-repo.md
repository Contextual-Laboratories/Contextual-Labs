---
title: Size Your Machine Before Indexing a Large Repo
domain: engine
category: how-to
tldr: Indexing is CPU-bound, not GPU-bound — more cores means a faster index, the daemon self-limits memory around 2.5GB before trimming, and disk usage scales with repo size since embeddings, graph, and blame all live in one local database.
order: 1
related:
  - engine/explanation/embedding-model-stack.md
  - engine/explanation/how-indexing-works.md
  - engine/how-to/speed-up-or-debug-a-slow-index-run.md
---

<Callout variant="tldr">
Embedding is CPU-only and uses every core available by default — indexing
speed scales with core count, not with having a GPU. The daemon
self-manages memory up to roughly 2.5GB before actively trimming. Disk
usage scales with repository size, since embeddings, the dependency
graph, and blame history all live together in one local database per
workspace.
</Callout>

## CPU cores, not a GPU

The embedding model runs on ONNX Runtime's CPU execution provider —
there's no GPU, MPS, or CUDA path to configure or benefit from. By
design, embedding uses every CPU core available on the machine during a
full index, so expect near-100% CPU utilization while `contextual index`
is running — that's expected behavior, not a runaway process. A
machine with more cores indexes proportionally faster; a GPU changes
nothing here.

## Memory: the daemon self-limits, it doesn't grow unbounded

The background daemon polls its own memory footprint every 30 seconds by
default and triggers a hard trim (garbage collection plus returning freed
memory pages to the OS) once it crosses a threshold — 800MB by default,
configurable via `[daemon].heap_trim_threshold_mb` in the global
`config.toml` if you need to raise or lower it. That default is set with
real headroom above the daemon's typical idle footprint (ONNX model plus
LanceDB buffers plus graph/nexus cache), specifically to catch
post-query buffer accumulation before it compounds. You shouldn't need to
plan for unbounded daemon memory growth on a long-running machine — if
RSS climbs well past that threshold and stays there, that's worth
investigating rather than assuming it's normal.

## Disk: scales with repo size, one store per workspace

Everything Contextual keeps about a workspace — chunk embeddings, the
dependency graph, blame/temporal history, decisions — lives in a single
LanceDB database under that workspace's `.contextual/` directory (see
`engine/reference/storage-schema-reference`). There's no separate budget
to plan per subsystem; disk usage for a workspace grows with the number
and size of files actually indexed, not with wall-clock time or index
count.

Individual files are capped: anything over 2MB is skipped outright, and
files over roughly 500KB fall back to line-based chunking instead of the
full tree-sitter split-merge pass (configurable via
`max_file_size_kb`). Neither cap affects disk sizing much on its own —
they exist to keep single pathological files (generated code, vendored
bundles) from dominating index time, not to bound total repo size.

## What actually determines index time

For a large repository, index time is driven by file count and total
byte volume more than any single hardware spec — see
`engine/how-to/speed-up-or-debug-a-slow-index-run` for what to check if
a specific run is slower than file count alone would explain, and
`engine/explanation/how-indexing-works` for what each pipeline stage
actually does with your CPU and disk.
