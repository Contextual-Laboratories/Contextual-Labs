---
title: "fetch"
domain: cli
category: reference
tldr: "contextual fetch pre-downloads the local CodeRankEmbed embedding model (~130MB) into ~/.contextual/models/ so the first contextual index doesn't pay that download inline."
order: 16
---

<Callout variant="tldr">
`contextual fetch` downloads the local embedding model weights ahead of
time. Optional — `index` will download them automatically on first use
otherwise — but useful if you want that ~130MB download out of the way
before your first real index run.
</Callout>

## Usage

```
contextual fetch
```

No arguments or flags. Requires `contextual setup` to have run already
(global install must exist).

<Terminal lines={[
  {command: "contextual fetch"},
  {output: "Downloading CodeRankEmbed INT8 (ONNX/fastembed)...\nDone.", muted: true}
]} />

If the model is already cached, `fetch` reports it as already present
and re-pins its BLAKE3 integrity hash rather than re-downloading.

<Callout variant="note">
The model is `nomic-ai/CodeRankEmbed`, a code-specialized embedding model
(MIT licensed), self-exported to INT8-quantized ONNX and downloaded from a
GitHub Release hosted on `Contextual-Labs`. Everything about this step
runs locally once downloaded — inference is CPU-only ONNX, and no network
call happens at query time afterward. The download itself is the only
network activity involved.
</Callout>

## See also

- `indexing/reference/sizing-and-machine-requirements`.
- `trust-and-privacy/reference/data-privacy` for the complete list of what does and
  doesn't leave your machine.
