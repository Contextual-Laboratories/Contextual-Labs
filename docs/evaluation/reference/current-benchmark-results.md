---
title: Current Benchmark Results
domain: evaluation
category: reference
tldr: Recall@10, NDCG@10, and MRR@10 for each golden repo on the primary target hardware profile, published once every repo has a verified baseline.
---

# Current Benchmark Results

Live retrieval-accuracy and performance numbers from Contextual's own eval suite, measured on real open-source repositories pinned in `eval/manifest.yml`. Primary hardware profile: `m2_air_8gb`. Published on demand, not on a fixed schedule — see [how we evaluate retrieval quality](/docs/evaluation#how-we-evaluate-retrieval-quality) for methodology.

## Per-repo results (m2_air_8gb)

| Repo | Language(s) | LOC | Recall@10 | NDCG@10 | MRR@10 | Median query latency | Indexing time | Peak RSS |
|---|---|---|---|---|---|---|---|---|
| angular | typescript | 1,353,561 | 64.3% | 44.8% | 39.0% | 59 ms | 95.3 min | 1351 MB |
| deno | rust, typescript, javascript | 1,418,350 | 87.8% | 63.7% | 56.0% | 60 ms | 44.3 min | 1685 MB |
| efcore | csharp | 1,639,357 | 91.4% | 74.6% | 69.4% | 176 ms | 84.6 min | 2250 MB |
| firebase-ios-sdk | swift, cpp | 957,620 | 54.3% | 43.4% | 40.0% | 18 ms | 28.8 min | 1305 MB |
| folly | cpp | 711,091 | 98.3% | 82.1% | 76.9% | 53 ms | 26.4 min | 1766 MB |
| gradle | java, kotlin, groovy | 183,893 | 94.3% | 70.6% | 63.1% | 127 ms | 28.6 min | 953 MB |
| hugo | go | 310,282 | 94.3% | 80.0% | 75.4% | 77 ms | 23.6 min | 1302 MB |
| ktor | kotlin | 290,372 | 97.1% | 77.5% | 71.1% | 36 ms | 10.1 min | 1145 MB |
| laravel | php | 545,386 | 88.0% | 71.9% | 66.9% | 70 ms | 31.3 min | 1740 MB |
| signal-ios | swift | 763,804 | 92.0% | 82.7% | 79.6% | 66 ms | 39.9 min | 1906 MB |
| zulip | python, typescript | 1,004,905 | 88.9% | 67.5% | 60.8% | 102 ms | 81.6 min | 1661 MB |

## Other hardware profiles

Accuracy only (these runs predate performance-metric capture); not directly comparable to the primary-profile table above.

| Repo | Profile | Language(s) | Recall@10 | NDCG@10 | MRR@10 |
|---|---|---|---|---|---|
| cpython | linux | python, c | 60.0% | 38.3% | 31.6% |
| eslint | linux | javascript | 80.0% | 54.8% | 46.8% |
| firebase-ios-sdk | linux | swift, cpp | 40.0% | 24.2% | 19.3% |
| folly | linux | cpp | 90.0% | 74.3% | 69.4% |
| gradle | linux | java, kotlin, groovy | 70.0% | 43.0% | 34.5% |
| hugo | linux | go | 40.0% | 22.5% | 16.7% |
| ktor | linux | kotlin | 70.0% | 38.6% | 28.6% |
| laravel | linux | php | 70.0% | 48.6% | 41.7% |
| rails | linux | ruby | 60.0% | 44.9% | 40.0% |
| signal-ios | linux | swift | 70.0% | 39.0% | 30.1% |

## Suite aggregate (m2_air_8gb)

| Repos measured | Median Recall@10 | Median tool-call latency | Indexing throughput |
|---|---|---|---|
| 12 | 90.2% | 66 ms | 313s / 100k LOC |

*Not yet baselined on any profile: spring-boot; incomplete baseline capture, excluded pending a rerun: eslint/m2_air_8gb.*
