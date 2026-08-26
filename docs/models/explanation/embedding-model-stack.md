---
title: The Embedding & Model Stack
domain: models
category: explanation
tldr: One local model — CodeRankEmbed, a code-specialized 137M-parameter bi-encoder — handles both code and documentation, runs CPU-only via ONNX Runtime, and never makes a network call at inference time. There is no reranking model.
order: 8
related:
  - retrieval/explanation/retrieval-pipeline.md
  - trust-and-privacy/reference/data-privacy.md
  - indexing/reference/sizing-and-machine-requirements.md
---

<Callout variant="tldr">
A single local embedding model — `nomic-ai/CodeRankEmbed` — handles code
and documentation both, running CPU-only via ONNX Runtime with no
network call at inference time. There is no reranking model in the
pipeline.
</Callout>

## One unified model, not several

**One embedding model** runs for both code chunks and documentation
chunks — not separate models per content type. There is no reranker in
the pipeline at all (see `retrieval/explanation/retrieval-pipeline` for
what ranking does instead).

## The model: CodeRankEmbed

Contextual embeds with **`nomic-ai/CodeRankEmbed`** — a 137M-parameter,
code-specialized bi-encoder, MIT licensed, producing 768-dimensional
vectors. It's trained specifically for code retrieval, rather than
being a general-purpose text embedding model applied to code — which is
what gives it strong accuracy on code-search queries specifically.

<Callout variant="note">
No official ONNX build of CodeRankEmbed exists upstream — its
architecture isn't supported by the standard automatic ONNX exporters —
so Contextual exports its own, quantized to INT8 for a smaller
on-disk/in-memory footprint. The exported artifact is hosted on a
GitHub Release under Contextual's own `Contextual-Labs` repository,
downloaded over plain HTTPS with no separate account or token needed.
Every download is verified against a pinned BLAKE3 hash before it's
trusted (see `contextual fetch` and `contextual doctor`'s Models
check).
</Callout>

## Asymmetric, query-side-only prefixing

CodeRankEmbed is an asymmetric embedding model: a query and the
document it should match aren't embedded identically. In this model's
case that asymmetry is narrower than in some other asymmetric models —
only the **query** side gets a task-instruction prefix prepended before
embedding ("represent this for searching relevant code"); code and
documentation chunks are embedded as-is, with no prefix on that side at
all. This is a model-specific detail, not a general rule — a different
embedding model could easily prefix both sides, or neither. It matters
here only in that it's a fixed part of the model's expected input
format, not a tunable setting.

## Runs entirely on your CPU, via ONNX Runtime

Embedding inference runs through `fastembed` on top of ONNX Runtime's
CPU execution provider — there is no GPU, MPS, or CUDA code path in the
current stack (see `models/explanation/why-local-cpu-only-inference`
for why). Embedding cost and memory behavior is governed by CPU
threading and ONNX's own batch-allocation behavior — see
`indexing/reference/sizing-and-machine-requirements` for what that
means in practice for indexing a large repository.

## No network call at inference time

Embedding a chunk of your code or a search query never leaves your
machine — the model's weights are downloaded once (`contextual fetch`)
and every subsequent embedding call runs against the local copy. See
`trust-and-privacy/reference/data-privacy` for the complete list of what
does and doesn't leave your machine, including the narrow cases
(license validation, update checks) that do make a network call.

## An engine update never leaves your index silently out of sync

If a future Contextual release ever changes the embedding model or
runtime in a way that makes previously-stored vectors incompatible,
that's handled automatically: an internal version marker is checked on
every `contextual index` run, and a mismatch triggers a full re-embed
of what's needed with no manual `--force` step required. You'd never
end up unknowingly querying against vectors from a different, no longer
comparable model.

## Model memory management is not hot-reload

The embedding model may be unloaded from memory after a period of
daemon idleness to free RAM, and transparently reloads on the next call
that needs it (a small one-time cost, well under a second). This is
ordinary memory management, not a hot-reload mechanism for code
changes — don't confuse it with the daemon restart behavior covered in
`mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload`.
