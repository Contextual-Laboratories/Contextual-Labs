---
title: Run the Eval Suite Yourself
domain: evaluation
category: how-to
tldr: How to run eval/run_eval.py or eval/run_all.py against one or all golden repos and read the resulting recall/latency report.
related:
  - evaluation/explanation/how-we-evaluate-retrieval-quality.md
  - evaluation/reference/golden-repo-list.md
---

<Callout variant="tldr">
`eval/run_eval.py` indexes one golden repo, runs its queries through
the real MCP path, and prints a full recall/latency report — `--repo
all` (or `eval/run_all.py`) does the same across every repo in
`eval/manifest.yml`, sequentially.
</Callout>

## Run one repo

```
python eval/run_eval.py --repo ktor --profile m2_air_8gb
```

`--profile` is required — it names an entry in
`eval/hardware_profiles.yml` (see
`evaluation/explanation/what-a-benchmark-number-means-here` for what a
profile is and why one, `m2_air_8gb`, is treated as canonical). This
checks out `ktor` at its pinned commit if it isn't already, indexes
it with the real `contextual` CLI, runs every `ktor` query in
`eval/queries.yml` through the real MCP shim, and prints a report like
this (real output, `ktor/m2_air_8gb`):

<Terminal lines={[
  {command: "python eval/run_eval.py --repo ktor --profile m2_air_8gb", output: "=== Contextual golden eval: ktor @ m2_air_8gb ===\nIndexing: 1952.0s, peak RSS 914.9MB, exit code 0\n\nRecall@10:  92.6%  (n=68, HARD GATE, tolerance 3pp vs baseline)\nNDCG@10:    0.780  (HARD GATE, tolerance 0.03 vs baseline)\nMRR@10:     0.730  (HARD GATE, tolerance 0.03 vs baseline)\nMedian query latency: 125.8ms\nResponse size: median 2533 tokens, p95 9353 tokens (soft regression check only, not gated)\n\nBy category:\n  co_change          recall=70.0%  ndcg=0.515  mrr=0.450  n=10  tokens=1314\n  cross_file         recall=100.0%  ndcg=0.690  mrr=0.583  n=4  tokens=10212\n  graph_at_time      recall=100.0%  ndcg=1.000  mrr=1.000  n=10  tokens=320\n  graph_impact       recall=100.0%  ndcg=0.852  mrr=0.800  n=10  tokens=4324\n  graph_traversal    recall=100.0%  ndcg=0.601  mrr=0.475  n=4  tokens=8154\n  graph_traverse     recall=100.0%  ndcg=0.864  mrr=0.817  n=20  tokens=2183\n  semantic_search    recall=100.0%  ndcg=0.750  mrr=0.667  n=2  tokens=4517\n  symbol_lookup      recall=75.0%  ndcg=0.750  mrr=0.750  n=4  tokens=4076\n  temporal           recall=75.0%  ndcg=0.608  mrr=0.562  n=4  tokens=3900\n\nktor/m2_air_8gb: Recall@10 92.6% vs baseline 92.6% (+0.0pp, tolerance -3.0pp) -- OK", muted: true}
]} />

Recall@10, NDCG@10, and MRR@10 are each hard-gated against the stored
baseline (see `evaluation/how-to/read-a-baseline-changelog-entry` for
the exact tolerances); indexing time and response size are printed as
soft, non-gating warnings only. Results are written to
`eval/results/<timestamp>_<profile>_<repo>/` as `recall.json` (full
per-query detail) and `indexing_stats.json`.

## Run every repo

```
python eval/run_all.py --profile m2_air_8gb
```

Drives `run_eval.py --update-baseline` once per repo in
`eval/manifest.yml`, sequentially — skips a repo that already has a
baseline for that profile unless `--force` is passed, and retries
automatically on transient global-indexing-lock contention. A subset
also works:

```
python eval/run_all.py --profile m2_air_8gb --repos hugo,folly,eslint
```

## Update the baseline

Add `--update-baseline` to a `run_eval.py` invocation to write that
run's numbers into `eval/baselines/recall_baseline.json` as the new
baseline for that repo/profile — a deliberate, reviewed action, not
something a routine run does by default:

```
python eval/run_eval.py --repo zulip --profile m2_air_8gb --update-baseline
```

## Fast iteration on a single layer

Three flags exist specifically to avoid paying for a full re-index
(10–150+ minutes on the larger repos) when only one layer changed.
None of these are compatible with `--update-baseline` — a partial
run's numbers aren't comparable to a full-run baseline and must never
overwrite one:

- **`--index-phase {semantic, graph}`** — index only the L1
  (chunk/embedding) or L2/L3 (graph/co-change) layer, passed straight
  through to `contextual index --semantic`/`--graph`.
- **`--query-categories {semantic, graph}`** — run only the query
  categories that layer actually answers (see
  `evaluation/reference/query-categories-and-tool-mapping` for the
  category split).
- **`--skip-index`** — skip indexing entirely and re-query an already
  indexed workspace as-is, for isolating a pure ranking/tool-code
  change.

```
python eval/run_eval.py --repo zulip --profile m2_air_8gb --index-phase graph --query-categories graph
python eval/run_eval.py --repo zulip --profile m2_air_8gb --skip-index --query-categories graph
```
