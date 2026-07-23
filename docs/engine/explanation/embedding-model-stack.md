---
title: The Embedding & Model Stack
domain: engine
category: explanation
tldr: One local, quantized ONNX embedding model handles both code and documentation, runs CPU-only via ONNX Runtime, and never makes a network call at inference time — there is no separate reranking model.
order: 8
related:
  - engine/explanation/retrieval-pipeline.md
  - engine/reference/data-privacy.md
  - engine/reference/sizing-and-machine-requirements.md
---

<Callout variant="tldr">
A single local, quantized ONNX embedding model handles code and
documentation both — not separate models per content type — running
CPU-only via ONNX Runtime, with no network call at inference time. There
is no reranking model in the stack at all; that stage was removed.
</Callout>

## One unified model, not several

Earlier in Contextual's development, the plan called for separate
embedding models per content type (a code-specialized model and a
text/documentation model) plus a cross-encoder reranker on top. The
shipped architecture is simpler than that: **one embedding model** runs
for both code chunks and documentation chunks, and there is no reranker
in the pipeline at all (see `engine/explanation/retrieval-pipeline` for
why that stage was removed rather than kept). If you've seen older
material describing a multi-model lineup, treat this page as current and
that as superseded.

<Callout variant="note">
Contextual does not publish the exact model checkpoint, its precise
quantization scheme, or its parameter count — this page describes the
architecture, not the specific weights. What's disclosed here (local,
CPU-only, one unified model, no reranker) is the complete, stable public
picture; it isn't a narrower version of some longer internal spec.
</Callout>

## Runs entirely on your CPU, via ONNX Runtime

Embedding inference runs through `fastembed` on top of ONNX Runtime's CPU
execution provider — there is no GPU, MPS, or CUDA code path in the
current stack. If you've seen older sizing guidance describing GPU
memory spikes or Apple Silicon MPS behavior for embedding, that describes
a prior architecture (PyTorch/sentence-transformers based) that was fully
removed. Current embedding cost and memory behavior is governed by CPU
threading and ONNX's own batch-allocation behavior instead — see
`engine/reference/sizing-and-machine-requirements` for what that means in
practice for indexing a large repository.

## No network call at inference time

Embedding a chunk of your code or a search query never leaves your
machine — the model's weights are downloaded once (`contextual fetch`)
and every subsequent embedding call runs against the local copy. See
`engine/reference/data-privacy` for the complete list of what does and
doesn't leave your machine, including the narrow cases (license
validation, update checks) that do make a network call.

## Model memory management is not hot-reload

The embedding model may be unloaded from memory after a period of
daemon idleness to free RAM, and transparently reloads on the next call
that needs it (a small one-time cost, well under a second). This is
ordinary memory management, not a hot-reload mechanism for code changes —
don't confuse it with the daemon restart behavior covered in
`engine/explanation/mcp-daemon-lifecycle-and-hot-reload`.
