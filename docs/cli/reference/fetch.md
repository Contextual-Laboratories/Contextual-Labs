---
title: "fetch"
domain: cli
category: reference
tldr: "contextual fetch pre-downloads the local embedding model (~130MB) into ~/.contextual/models/ so the first contextual index doesn't pay that download inline."
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
  {output: "Downloading Nomic Embed v1.5-Q (ONNX/fastembed)...\nDone.", muted: true}
]} />

If the model is already cached, `fetch` reports it as already present
and re-verifies its integrity hash rather than re-downloading.

<Callout variant="note">
Everything about this step runs locally once downloaded — the model is
CPU-only ONNX, and no network call happens at query time afterward. The
download itself is the only network activity involved.
</Callout>

## See also

- `engine/reference/sizing-and-machine-requirements`.
- `engine/reference/data-privacy` for the complete list of what does and
  doesn't leave your machine.
