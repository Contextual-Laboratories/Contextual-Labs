# Models

The embedding model stack: what runs, where, and why it's local and CPU-only.

- `models/explanation/embedding-model-stack` — the model itself
  (CodeRankEmbed), why there's one model not several, why there's no
  reranker.
- `models/explanation/why-local-cpu-only-inference` — why there's no
  GPU/MPS/CUDA path, and what that trades off.
- `models/reference/model-configuration-reference` — what's actually
  configurable (batch size, ONNX Runtime env vars) versus fixed.
