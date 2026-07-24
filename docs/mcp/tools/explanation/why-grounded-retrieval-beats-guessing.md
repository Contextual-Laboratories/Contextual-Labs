---
title: Why grounded retrieval beats a model guessing
domain: mcp-tools
category: explanation
tldr: An AI model's training data is frozen and generic — it has never seen your repository. Contextual's tools return real, current chunks and graph edges from your actual code, so an answer can be checked against something real instead of trusted on faith.
order: 2
---

<Callout variant="tldr">
A language model's training data has a cutoff and was never trained on
your specific repository. Asking it to answer questions about your code
from memory means it's pattern-matching against "code like this
usually," not your actual code. Contextual's tools exist to replace that
guess with a real, current lookup.
</Callout>

## The actual mechanism, not a generic pitch

Search combines three real signals — BM25 keyword matching, dense
vector similarity, and trigram matching — fused by Reciprocal Rank
Fusion, with an exact-match override and MMR diversity selection to
avoid returning ten near-duplicate chunks. Cross-encoder reranking was
tried and removed after testing found no precision benefit — worth
saying plainly rather than pretending every fashionable retrieval
technique is in the pipeline.

The graph tools add a second, structurally different signal: not "what
looks similar," but "what is actually connected" — real calls, imports,
and inheritance relationships extracted from parsing your code, not
inferred from proximity in a vector space.

## What this actually buys you

A model that only pattern-matches from training data will confidently
describe a function that doesn't exist, or describe one that does exist
incorrectly, with no way for you to tell the difference from the
prose alone. A model that calls `search` or `get_file_content` first is
citing something you can go check yourself — a specific file, a
specific line range, a specific commit.

The graph tools extend this to questions training data structurally
cannot answer at all: "what breaks if I delete this" isn't a fact that
exists anywhere for a model to have memorized — it has to be computed,
per-repository, every time, from your actual dependency structure.

## See also

- `mcp/tools/explanation/tool-taxonomy`.
- `retrieval/explanation/retrieval-pipeline` for the full mechanism.
