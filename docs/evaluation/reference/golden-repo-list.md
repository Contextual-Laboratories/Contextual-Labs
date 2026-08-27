---
title: Golden Repo List
domain: evaluation
category: reference
tldr: The 15 pinned repositories used for evaluation, their primary language(s), license, and pinned commit.
related:
  - evaluation/explanation/why-these-golden-repos.md
  - evaluation/reference/current-benchmark-results.md
---

<Callout variant="tldr">
15 real, pinned-commit repositories back every benchmark number
Contextual publishes. "Indexed files" is Contextual's own indexer
filter applied to that repo — not the raw file count on GitHub. "Eval
checkout" is the on-disk size of this suite's own local working copy,
not the upstream project's real repository size.
</Callout>

Full source, license, and pin for each repo lives in
`eval/manifest.yml`; see `evaluation/explanation/why-these-golden-repos`
for how these 15 were selected.

| Repo | Language(s) | License | Pinned commit | Indexed files | LOC | Eval checkout |
|---|---|---|---|---|---|---|
| angular | typescript | MIT | `f48d276` | 8,475 | 1,353,561 | 192M |
| cpython | python, c | PSF-2.0 | `0023d5b` | 3,674 | pending¹ | 112M |
| deno | rust, typescript, javascript | MIT | `7073436` | 5,323 | 1,418,350 | 171M |
| efcore | csharp | MIT | `937552f` | 5,560 | 1,639,357 | 189M |
| eslint | javascript | MIT | `c5963f7` | 2,068 | 617,507 | 324M |
| firebase-ios-sdk | swift, cpp | Apache-2.0 | `2712a22` | 3,801 | 957,620 | 445M |
| folly | cpp | Apache-2.0 | `8a584b5` | 2,440 | 710,228 | 932M |
| gradle | java, kotlin, groovy | Apache-2.0 | `4201bbe` | 3,690 | 179,595 | 862M |
| hugo | go | Apache-2.0 | `a198116` | 2,116 | 302,900 | 558M |
| ktor | kotlin | Apache-2.0 | `79d4dbc` | 2,587 | 287,057 | 455M |
| laravel | php | MIT | `3093ff3` | 3,161 | 542,084 | 1.7G |
| rails | ruby | MIT | `35ee781` | 3,905 | 663,538 | 2.9G |
| signal-ios | swift | GPL-3.0 | `265ee50` | 3,402 | 758,132 | 796M |
| spring-boot | java | Apache-2.0 | `4c34688` | 9,090 | 864,870 | 1.2G |
| zulip | python, typescript | Apache-2.0 | `66c42d3` | 5,990 | 1,004,905 | 112M |

**15 repos, ~11GB total eval-checkout footprint, 12 distinct languages
across 4 genuine polyglot pairings** (deno, gradle, firebase-ios-sdk,
cpython — see `evaluation/explanation/why-these-golden-repos`).

¹ cpython doesn't yet have a verified baseline on the primary
`m2_air_8gb` hardware profile — see
`evaluation/reference/current-benchmark-results`. Its indexed-file
count is real (measured at selection time); LOC is populated once its
`m2_air_8gb` baseline run completes.

## Reading "indexed files" correctly

This column is Contextual's own indexer filter (`.gitignore` +
`.contextualignore` + non-source exclusions) applied to each repo — it
answers "how big is this repo from Contextual's point of view," not
"how many files does this project actually have." The gap between the
two can be large: gradle's real checkout has roughly 28,800 files on
disk, but only 3,690 of them are what the indexer would actually
touch. Comparing "indexed files" across repos in this table is
meaningful; comparing it against a number you'd get from `find` or
GitHub's own file browser on the same repo is not.
