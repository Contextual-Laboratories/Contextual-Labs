---
title: What a Benchmark Number Means Here
domain: evaluation
category: explanation
tldr: Every result is tagged to a specific hardware profile and only ever compared against a baseline from that same profile — CI-runner numbers are a regression signal only, never a stand-in for a real hardware claim.
related:
  - evaluation/reference/current-benchmark-results.md
  - evaluation/explanation/how-we-evaluate-retrieval-quality.md
---

<Callout variant="tldr">
A recall number on its own means nothing without knowing what hardware
it was measured on — Contextual's eval suite tags every result to a
named hardware profile and never compares across profiles.
</Callout>

## Every result is tagged to a profile

`eval/hardware_profiles.yml` defines a small set of named machine
profiles a run can be tagged with — display name, OS, and RAM. A
baseline is stored per `<repo>/<profile>` pair, and a fresh run is only
ever compared against the baseline recorded under that *same* profile.
An 8GB machine and a 16GB machine have genuinely different acceptable
latency and memory envelopes; comparing a result from one against a
baseline from the other would produce a meaningless delta.

## One profile is primary

Of the profiles defined, exactly one — `m2_air_8gb` (MacBook Air M2,
8GB RAM) — is marked required. Every recall or regression baseline has
to be established here at minimum before it's trusted for a launch
claim. The others (a 16GB Apple Silicon MacBook Pro, Windows, Linux)
are secondary: run opportunistically when the hardware is available,
useful for spotting a platform-specific issue, but not what a
published accuracy number is expected to come from.

## CI hardware is a regression signal, not a hardware claim

A GitHub Actions hosted runner (`ci_hosted`) is its own profile, used
only for a lightweight Recall@10 check on every PR — and explicitly
**not** a stand-in for any of the physical profiles above. Hosted
runners have no guaranteed RAM or CPU floor, so a `ci_hosted` number is
useful for "did this change make retrieval quality worse," but it's
never cited as a hardware-specific number (e.g. "measured on an M2
8GB") — that claim only ever comes from an actual run tagged
`m2_air_8gb`.

## What "trusted" means in practice

A number only makes it into
`evaluation/reference/current-benchmark-results` once it's a verified
`m2_air_8gb` baseline — the output of a real, complete indexing +
query run against the pinned commit, recorded in
`eval/baselines/recall_baseline.json` and reproducible by re-running
`eval/run_eval.py` against the same repo and profile (see
`evaluation/how-to/run-the-eval-suite-yourself`). A repo without one
yet is marked pending rather than backfilled with a number from a
different profile.
