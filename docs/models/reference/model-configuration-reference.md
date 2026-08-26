---
title: Model configuration reference
domain: models
category: reference
tldr: "The embedding model itself isn't configurable — there's exactly one, chosen for you. What is configurable: batch size for higher-RAM machines, and the underlying ONNX Runtime threading/memory-arena environment variables for advanced tuning."
order: 10
related:
  - models/explanation/embedding-model-stack.md
  - models/explanation/why-local-cpu-only-inference.md
  - cli/reference/general/fetch.md
---

<Callout variant="tldr">
There's no setting to swap in a different embedding model — Contextual
ships with exactly one (see `models/explanation/embedding-model-stack`),
chosen and validated for you. What you can configure is narrower:
inference batch size, and (for advanced use) the ONNX Runtime
environment variables the embedder itself sets defaults for.
</Callout>

## What's not configurable, on purpose

There is no config key or environment variable to point Contextual at
a different embedding model, a different vector dimension, or a
reranking stage — none of those exist as pluggable options. The model
stack is a fixed part of the shipped architecture, validated against
this specific model's behavior (its prefix contract, its pooling, its
integrity hash). Swapping it out from outside the codebase isn't a
supported configuration path.

## Where the model lives on disk

Model weights are downloaded once, into a single global directory
shared by every workspace on the machine — `~/.contextual/models/` —
not duplicated per-repository. `contextual fetch` downloads them
ahead of time; otherwise the first `contextual index` run downloads
them inline. A BLAKE3 hash of the downloaded file is pinned locally and
re-checked on every subsequent load — `contextual doctor`'s Models
check surfaces a mismatch (corruption or tampering) as a failure rather
than silently loading a bad file.

## Batch size

Inference batch size — how many chunks get embedded in one ONNX call —
is tuned internally for safe, bounded memory use on Contextual's stated
8GB-RAM floor target device (see
`models/explanation/why-local-cpu-only-inference` for why that floor
governs so much of this stack's tuning). Machines with more RAM to
spare can raise it via an environment variable:

```
CONTEXTUAL_EMBED_BATCH=32 contextual index
```

<Callout variant="warning">
Raising this is a real memory/throughput tradeoff, not a free win — the
default is deliberately conservative for the floor device, not for
your specific machine. Only raise it if you know your machine has
comfortable RAM headroom beyond what a normal indexing run already
uses, and treat a raised value as something to revert if you see memory
pressure during a large index run.
</Callout>

## ONNX Runtime threading and memory-arena variables

The embedder sets a handful of ONNX Runtime and OpenMP environment
variables at load time — `OMP_NUM_THREADS`, `OPENBLAS_NUM_THREADS`,
`ORT_MEMORY_ARENA_EXTEND_STRATEGY`, `ORT_DISABLE_MEMORY_PATTERN` — to
control CPU thread count and the memory-arena allocator's behavior
(see `models/explanation/embedding-model-stack` for why arena behavior
matters on the 8GB floor). These are set as *defaults*, not forced
values: if any of them is already set in your environment before
Contextual starts, your value is respected instead of being overridden.
This is an advanced-tuning escape hatch, not something normal use needs
to touch — the defaults are what every documented sizing/performance
figure elsewhere in these docs assumes.

## Dimension truncation (Matryoshka) — supported, not enabled

The embedding model supports Matryoshka-style dimension truncation (using
a prefix of the full vector instead of all 768 dimensions, for a smaller
on-disk footprint). Contextual ships with this **off** — the full
768-dimensional vector is used — because the measured retrieval-quality
cost of truncating wasn't judged worth the storage savings on the
workloads Contextual targets. There is currently no supported
user-facing setting to turn truncation on; it exists as an internal
capability the embedder is built to support, not a shipped option.

## A future model change would never mix old and new vectors silently

See "An engine update never leaves your index silently out of sync" in
`models/explanation/embedding-model-stack` for how that's detected and
handled automatically by a plain `contextual index` run.

## See also

- `cli/reference/general/fetch` — pre-downloading the model.
- `cli/reference/general/doctor` — the Models health check.
- `indexing/reference/sizing-and-machine-requirements` — what indexing
  cost actually looks like on real repository sizes.
