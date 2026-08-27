---
title: Read a Baseline Changelog Entry
domain: evaluation
category: how-to
tldr: How to interpret an eval/baselines/CHANGELOG.md entry and the tolerance behind it — a result only regresses if Recall@10 drops more than 3 percentage points or NDCG@10/MRR@10 drop more than 0.03 versus the same repo/hardware-profile baseline.
related:
  - evaluation/how-to/run-the-eval-suite-yourself.md
  - evaluation/explanation/what-a-benchmark-number-means-here.md
---

<Callout variant="tldr">
Every `--update-baseline` run adds a dated entry to
`eval/baselines/CHANGELOG.md` explaining why. A result only counts as
a regression against the stored baseline if Recall@10 drops more than
3 percentage points, or NDCG@10/MRR@10 drop more than 0.03 — smaller
moves are normal run-to-run noise, not a failure.
</Callout>

## What one entry looks like

Each `eval/baselines/CHANGELOG.md` entry is one dated bullet naming
the repo/profile it updates, the real indexed file/chunk count and
indexing duration, the three headline metrics, and a pointer to the
full result directory under `eval/results/`:

```
- **2026-08-15** — `example-repo/m2_air_8gb`: first baseline for this
  repo/profile. 4,200 files / 18,300 chunks indexed (1,840.5s, peak RSS
  1,120MB), recall@10 88.5%, ndcg@10 0.712, mrr@10 0.664 (n=70). Full
  run in `eval/results/20260815T093000Z_m2_air_8gb_example-repo/`.
```

The full detail behind any entry — every individual query's hit/miss,
rank, and latency — lives in that referenced `recall.json`, not in the
changelog itself; the changelog entry is the human-readable summary of
why the number changed, not the raw data.

## The exact tolerances

A fresh run is compared against the existing baseline for the same
`<repo>/<hardware-profile>` pair on three metrics, all hard-gated —
any one failing fails the whole comparison:

| Metric | Tolerance | Passes if |
|---|---|---|
| Recall@10 | 3 percentage points | current ≥ baseline − 3pp |
| NDCG@10 | 0.03 | current ≥ baseline − 0.03 |
| MRR@10 | 0.03 | current ≥ baseline − 0.03 |

NDCG@10 and MRR@10 use the same 0–1 scale as Recall@10 (a rank-quality
score, not a raw hit rate), so the same absolute tolerance is applied
to all three rather than inventing a separate methodology per metric.

Two more figures — indexing duration and median response size — are
tracked in the same entry but are **soft-only**: a run that's more
than 1.5x slower to index, or whose median response grew more than
1.3x in token count, prints a warning but never fails the comparison.
Indexing wall-clock time in particular is a property of the code *and*
the machine it happened to run on, not the code alone, which is why
only the three accuracy metrics are hard-gated.

## Why a baseline update needs a changelog line at all

A baseline file on its own is just a JSON blob of numbers — it can't
say *why* a number moved. Every `--update-baseline` run is expected to
add a line explaining the change: a first-time baseline for a
newly-added repo, a refresh after advancing a repo's pinned commit, or
a correction after finding and fixing a real bug in the eval harness
itself. That's what makes a large jump in a number trustworthy rather
than suspicious — the changelog is the record of *why* it's expected.
