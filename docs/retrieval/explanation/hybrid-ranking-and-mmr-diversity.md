---
title: Hybrid ranking and MMR diversity
domain: retrieval
category: explanation
tldr: "How BM25, dense embeddings, and trigram matching combine via weighted Reciprocal Rank Fusion, and exactly how the MMR diversity pass — including a structural near-duplicate penalty — decides what to keep."
order: 7
related:
  - retrieval/explanation/retrieval-pipeline.md
  - retrieval/reference/retrieval-configuration-reference.md
  - models/explanation/embedding-model-stack.md
---

<Callout variant="tldr">
Fusion is weighted Reciprocal Rank Fusion, not a raw-score blend: each
method's contribution to a candidate's score is its configured weight
divided by `rrf_k` plus its rank in that method's own results — a
candidate that multiple methods agree on accumulates score from each of
them. MMR then greedily selects a diverse top-k by trading off
normalized relevance against similarity to what's already been picked,
including a separate penalty for chunks that are structurally
near-identical even when their embeddings differ.
</Callout>

## Reciprocal Rank Fusion, weighted

Each of the three methods described in
`retrieval/explanation/retrieval-pipeline` (BM25, dense, trigram)
returns its own ranked candidate list, independently. Fusion combines
them with this formula, applied per candidate, per method that returned
it:

```
score(chunk) = Σ  weight(method) / (rrf_k + rank(chunk, method))
```

A chunk that only one method returned gets a score from that method
alone. A chunk that two or three methods agree on accumulates score
from each — this is why fusion tends to reward consensus across
signals, not just a single method's top pick. The default weighting is
dense-heaviest, then BM25, then trigram — reflecting that semantic
similarity is usually the strongest single signal for a natural-language
query, with the other two catching what it misses (exact identifiers,
fuzzy/partial identifier matches). See
`retrieval/reference/retrieval-configuration-reference` for the exact
default weight and `rrf_k` values, and how to change them.

<Callout variant="note">
`rrf_k` is a rank-fusion damping constant, not a candidate-count limit —
raising it flattens the difference between a rank-1 and a rank-10 hit
within one method (reduces how much a single method's top pick
dominates); lowering it sharpens that difference. It's the same role
`k` plays in Reciprocal Rank Fusion generally, not something
Contextual-specific.
</Callout>

## MMR: relevance traded against diversity, not relevance alone

After fusion (and the exact-match override described in
`retrieval/explanation/retrieval-pipeline`), the fused candidate list
still tends to cluster — several near-duplicate chunks from the same
file or symbol, all individually relevant. The MMR pass re-orders
candidates by:

```
MMR(d) = λ · relevance(d)  −  (1 − λ) · max_similarity(d, already_selected)
```

`relevance(d)` is each candidate's fused RRF score, **min-max
normalized to [0, 1] across the candidate set** before this formula
runs — not a fresh cosine similarity to the query computed at this
stage, which means every upstream signal (BM25, dense, trigram, the
exact-match override, the structural-duplicate penalty below) is
already folded into it by the time MMR runs. `max_similarity` is the
highest cosine similarity between this candidate's embedding and any
chunk already selected — so a candidate very similar to something
already picked is penalized, even if it's individually relevant. `λ`
(lambda) is the relevance/diversity tradeoff; Contextual's default
leans toward relevance — see the reference page for the exact value and
how to adjust it toward more or less diversity.

Selection is greedy, one candidate at a time: at each step, every
remaining candidate's MMR score is computed against the *currently
selected set*, the best-scoring one is added, and the process repeats.
This means diversity is evaluated against what's actually been chosen so
far, not against the whole candidate pool at once.

### Hard quotas, checked before scoring

Independent of the MMR score itself, two hard caps are enforced as a
pre-filter: a maximum number of chunks per file, and a maximum number
of chunks per symbol. A candidate that would exceed either quota is
skipped from consideration entirely at that step, regardless of how
high its MMR score would otherwise be — these are hard limits, not soft
preferences the score can override.

## The structural near-duplicate penalty

Two chunks can be far apart in embedding space (different variable
names, different comments, different string literals) while having
almost identical *shape* — the same nested-if/loop/function structure —
and MMR's cosine-similarity diversity term alone won't catch that,
since it only looks at embeddings. A separate pass addresses this
directly, before MMR selection runs:

1. Each candidate's **structural token sequence** is extracted —
   language keywords and punctuation only (`if`, `for`, `class`, `{`,
   `;`, and their equivalents across every language the chunker
   supports), stripped of variable names, string contents, and comments.
2. That sequence is fingerprinted with **MinHash** over overlapping
   5-token shingles, so two chunks with a very similar structural
   "shape" produce a very similar fingerprint.
3. Every candidate pair is compared via estimated **Jaccard
   similarity** on those fingerprints. Pairs at or above a fixed
   similarity threshold are treated as structurally near-duplicate.
4. Rather than being removed outright, a near-duplicate candidate has
   its relevance score reduced by a fixed penalty factor before MMR
   selection runs — the first candidate in a near-duplicate group is
   left untouched; later ones in the same group are penalized, making
   MMR less likely (not impossible) to select more than one of them.

This runs once per query, over the fused candidate pool (typically well
under 50 chunks), and is independent of `mmr_max_chunks_per_file` /
`mmr_max_chunks_per_symbol` — two structurally-near-identical helper
functions defined in *different* files or under different symbol names
would sail past both of those quotas untouched; this pass is what
catches that case instead.

## See also

- `retrieval/explanation/retrieval-pipeline` — the full pipeline this
  page assumes: BM25/dense/trigram, the exact-match override, and why
  there's no reranking stage.
- `retrieval/reference/retrieval-configuration-reference` — every
  tunable field's exact default and what changing it does.
- `retrieval/how-to/tune-retrieval-quality-for-your-codebase`.
