---
title: Current Benchmark Results
domain: evaluation
category: reference
tldr: Recall@10, NDCG@10, and MRR@10 for each golden repo with a verified baseline on the primary hardware profile.
related:
  - evaluation/reference/golden-repo-list.md
  - evaluation/explanation/what-a-benchmark-number-means-here.md
---

# Current Benchmark Results

Live retrieval-accuracy and performance numbers from Contextual's own eval suite, measured on real open-source repositories pinned in `eval/manifest.yml`. Primary hardware profile: `m2_air_8gb` — see `evaluation/explanation/what-a-benchmark-number-means-here` for why only this profile's numbers are published as a hardware claim. Published on demand, not on a fixed schedule — see `evaluation/explanation/how-we-evaluate-retrieval-quality` for methodology.

## Per-repo results (m2_air_8gb)

| Repo | Language(s) | LOC | Recall@10 | NDCG@10 | MRR@10 | Median query latency | Indexing time | Peak RSS |
|---|---|---|---|---|---|---|---|---|
| angular | typescript | 1,322,448 | 82.9% | 59.7% | 52.4% | 87 ms | 112.2 min | 1724 MB |
| deno | rust, typescript, javascript | 1,418,350 | 87.8% | 63.7% | 56.0% | 60 ms | 44.3 min | 1685 MB |
| efcore | csharp | 1,639,357 | 91.4% | 74.6% | 69.4% | 176 ms | 84.6 min | 2250 MB |
| eslint | javascript | 617,507 | 83.3% | 69.1% | 64.4% | 65 ms | 31.6 min | 877 MB |
| firebase-ios-sdk | swift, cpp | 957,620 | 54.3% | 43.4% | 40.0% | 18 ms | 28.8 min | 1305 MB |
| folly | cpp | 710,228 | 77.6% | 65.4% | 61.2% | 76 ms | 60.9 min | 1580 MB |
| gradle | java, kotlin, groovy | 179,595 | 91.4% | 70.2% | 63.4% | 94 ms | 31.8 min | 884 MB |
| hugo | go | 302,900 | 97.1% | 79.6% | 73.7% | 109 ms | 28.3 min | 1192 MB |
| ktor | kotlin | 287,057 | 92.6% | 78.0% | 73.0% | 126 ms | 32.5 min | 915 MB |
| laravel | php | 542,084 | 98.0% | 83.7% | 79.0% | 95 ms | 47.9 min | 1512 MB |
| rails | ruby | 663,538 | 34.3% | 27.8% | 25.7% | 89 ms | 87.6 min | 1802 MB |
| signal-ios | swift | 758,132 | 80.0% | 73.6% | 71.5% | 101 ms | 80.3 min | 1668 MB |
| spring-boot | java | 864,870 | 79.7% | 57.5% | 50.5% | 152 ms | 151.0 min | 1850 MB |
| zulip | python, typescript | 889,369 | 86.7% | 64.2% | 56.9% | 140 ms | 107.8 min | 1368 MB |

*Pending: `cpython` doesn't yet have a verified `m2_air_8gb` baseline.*

## Suite aggregate (m2_air_8gb)

| Repos measured | Median Recall@10 | Median tool-call latency | Indexing throughput |
|---|---|---|---|
| 14 | 85.0% | 94.5 ms | 545.8s / 100k LOC |

## Response size (tokens)

Newer metric, added to the eval harness after most of the table above
was first measured — not every repo has been re-run since, so coverage
is partial. Median and p95 are the number of tokens in each MCP tool
response across the query suite for that repo.

| Repo | Median response tokens | p95 response tokens |
|---|---|---|
| angular | 4,520.5 | 8,856.6 |
| eslint | 4,965.0 | 7,477.3 |
| folly | 3,459.5 | 7,423.2 |
| gradle | 3,295.5 | 9,527.5 |
| hugo | 4,771.5 | 7,928.9 |
| ktor | 2,533.0 | 9,352.8 |
| laravel | 4,042.5 | 8,809.0 |
| rails | 140.5 | 13,571.1 |
| signal-ios | 5,015.5 | 9,171.2 |
| spring-boot | 4,087.0 | 11,919.6 |
| zulip | 3,973.5 | 11,254.5 |

*Not yet measured: `deno`, `efcore`, `firebase-ios-sdk`, `cpython`.*

Suite-wide median response tokens (across the 11 repos measured so far): **4,042.5**.

Every figure in all three tables is a hard fact pulled directly from
`eval/baselines/recall_baseline.json` — not rounded estimates or
marketing figures. See `evaluation/how-to/run-the-eval-suite-yourself`
to reproduce any of these numbers yourself.
