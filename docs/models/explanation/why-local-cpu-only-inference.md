---
title: Why local, CPU-only inference
domain: models
category: explanation
tldr: "Embedding inference runs locally on CPU via ONNX Runtime, with no GPU/MPS/CUDA path — a deliberate design choice for predictable, bounded memory use on modest hardware, not a feature that's simply unbuilt."
order: 9
related:
  - models/explanation/embedding-model-stack.md
  - indexing/reference/sizing-and-machine-requirements.md
---

<Callout variant="tldr">
Contextual's embedding model runs CPU-only, via ONNX Runtime, with no
GPU/MPS/CUDA code path. This is a deliberate design choice, not a gap:
GPU-accelerated execution paths make peak memory use harder to bound
predictably, and predictable memory use matters more than raw
throughput on the modest hardware Contextual is built to run well on.
</Callout>

## Built to run well on modest hardware

Contextual is built to run well on ordinary developer hardware, not
just a high-spec workstation — the stated floor target is an 8GB
unified-memory machine. On a machine with that little headroom, the
difference between "peak memory use is bounded and predictable" and
"peak memory use depends on batch size, sequence length, and a runtime's
own internal allocator behavior" is the difference between indexing
finishing cleanly and the machine swapping itself into unusability
mid-run. That's the design priority this page is about: predictable,
bounded memory over raw speed.

## What CPU-only ONNX Runtime buys

Running ONNX Runtime's CPU execution provider exclusively means
indexing cost is governed by CPU threading and ONNX's own explicit
memory-arena controls — settings Contextual sets directly, rather than
trusting a GPU runtime's own internal allocator to behave predictably
under load. This is slower in principle than a working GPU-accelerated
path would be, but it's bounded and predictable in a way that matters
more on modest hardware than throughput does.

<Callout variant="note">
GPU-accelerated execution paths were evaluated for this workload and
set aside: on the hardware class Contextual targets, they introduced
memory behavior that couldn't be bounded as tightly as the CPU
execution provider's explicit arena controls allow. This isn't a
permanent architectural ceiling — it reflects where the tradeoff
currently lands for the hardware floor Contextual is built around.
</Callout>

## What this means in practice

Embedding cost scales with CPU threading and repository size, not GPU
availability — a machine with a powerful GPU gets no speed benefit from
it for indexing, and a machine with no GPU at all is running the exact
same code path everyone else is. See
`indexing/reference/sizing-and-machine-requirements` for what that
means on real repository sizes, and
`models/explanation/embedding-model-stack` for the model itself.

## Not a privacy mechanism

This page is about hardware and memory behavior, not about what leaves
your machine — CPU-only execution doesn't make Contextual any more or
less local than a GPU-accelerated path would. See
`trust-and-privacy/reference/data-privacy` for the actual privacy
boundary: there is no remote embedding call regardless of which
execution provider runs it.
