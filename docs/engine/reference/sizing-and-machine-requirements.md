---
title: Sizing & Machine Requirements
domain: engine
category: reference
tldr: CPU-only, no GPU required — indexing speed scales with core count, the daemon self-trims memory around 800MB by default, individual files over 2MB are skipped, and Python 3.11 through 3.14 are supported.
order: 6
related:
  - engine/how-to/size-your-machine-before-indexing-a-large-repo.md
  - engine/explanation/embedding-model-stack.md
  - engine/reference/platform-support.md
---

<Callout variant="tldr">
No GPU, no MPS, no CUDA anywhere in the stack — embedding is CPU-only via
ONNX Runtime and uses every available core by default. Memory is
self-managed by the daemon, not something you need to provision for
manually. Python 3.11–3.14 are supported.
</Callout>

## Compute

Embedding inference runs on ONNX Runtime's CPU execution provider only.
There is no GPU code path to configure, benefit from, or troubleshoot —
a machine with more CPU cores indexes proportionally faster; a discrete
GPU changes nothing. Expect near-full CPU utilization across all cores
during a full index; that's expected, not a runaway process.

## Memory

The daemon polls its own memory footprint every 30 seconds and triggers
a hard trim (garbage collection plus returning freed pages to the OS)
once it crosses a configurable threshold — 800MB by default
(`[daemon].heap_trim_threshold_mb` in the global config, see
`engine/reference/configuration-reference`), set with real headroom
above the daemon's typical idle footprint. This is active, ongoing
self-management, not a hard cap you need to size your machine against in
advance — if RSS climbs well past that threshold and stays elevated,
that's worth investigating, but normal operation is self-bounded.

## Disk

Everything Contextual stores about a workspace — embeddings, graph,
blame/temporal history, decisions, caches — lives in one LanceDB
database under that workspace's `.contextual/` directory (see
`engine/reference/storage-schema-reference`). Disk usage tracks
repository size directly: more and larger indexable files means more
disk used, with no separate per-subsystem budget to plan around.

Two file-level caps limit worst-case cost from a single pathological
file, not overall repo size: files over 2MB are skipped entirely, and
files over roughly 500KB (configurable via `max_file_size_kb`) fall back
to line-based chunking instead of the full tree-sitter split-merge pass.

## Software requirements

Python 3.11 through 3.14 are supported and tested in CI across macOS,
Linux, and Windows. See `engine/reference/platform-support` for
per-platform behavior differences beyond the Python version itself.

## What actually determines whether an index run feels slow

Total file count and byte volume drive index time far more than any
single hardware spec threshold — see
`engine/how-to/size-your-machine-before-indexing-a-large-repo` and
`engine/how-to/speed-up-or-debug-a-slow-index-run` for what to check on a
specific slow run, and `engine/explanation/how-indexing-works` for what
each pipeline stage is actually doing with your CPU, memory, and disk.
