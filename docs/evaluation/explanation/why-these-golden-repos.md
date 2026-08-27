---
title: Why These Golden Repos
domain: evaluation
category: explanation
tldr: 15 real, actively-maintained open-source repos pinned to exact commits, one per major language or genuine polyglot slot, each chosen by measuring actual indexed file count against a target size band rather than by reputation alone.
related:
  - evaluation/reference/golden-repo-list.md
  - evaluation/explanation/how-we-evaluate-retrieval-quality.md
---

<Callout variant="tldr">
Every accuracy number Contextual publishes comes from 15 real,
actively-maintained open-source repositories, pinned to an exact
commit and selected by actually measuring what Contextual's own
indexer would index — not by picking well-known project names and
hoping they land at a reasonable size.
</Callout>

## Pinned, not tracked

Each golden repo is locked to one exact commit SHA in
`eval/manifest.yml`, not "whatever `main` currently is." A benchmark
run today and one six months from now need to be testing the same
corpus, or a recall number stops meaning anything — a repo's own code
churns, and an apparent accuracy swing could just as easily be "the
upstream project restructured its directory layout" as "Contextual got
better or worse." Advancing a pin is a deliberate, occasional action —
re-checkout, re-run the full suite, and record why in
`eval/baselines/CHANGELOG.md` — never something that happens
automatically on a schedule or a fresh clone.

## Measured, not guessed

The size of a repo, for eval purposes, isn't its GitHub star count or
its raw file count on disk — it's how many files Contextual's own
indexer filter (`FileProcessor.discover_files()`) would actually
decide to index, after `.gitignore`/`.contextualignore` exclusions and
non-source content are filtered out. That number is what gets measured
against a target size band before a repo is accepted into the suite,
and it's what's recorded per repo in
`evaluation/reference/golden-repo-list`.

This matters because the two numbers can differ enormously. Gradle's
raw checkout has roughly 28,800 files, but only about 3,690 of them
are what the real indexer would touch — the rest is non-indexable
test-fixture and sample-project content the filter correctly excludes.
Measuring the filtered count, not the raw one, is what keeps every
repo in the suite comparably sized from Contextual's own point of
view, even though their raw sizes on GitHub vary wildly.

This measure-first discipline also means a repo that looks obviously
right on paper sometimes isn't. The original candidate for the
Kotlin slot was a well-known Android reference sample app — it
measured at only a few hundred filtered files, too small and too thin
to be a realistic production codebase, so a larger real framework
(Ktor) took the slot instead. The original TypeScript candidate
measured well under the target band for the same reason. In both
directions — a "safe" pick that turns out too small, or a "risky" pick
(a core C++ library, a full framework monorepo) that turns out to
measure fine — the actual filtered count decides, not the assumption
going in.

## Two kinds of slots

Most golden repos fill a single-language slot: one dominant language,
chosen as the strongest real production codebase available at the
right scale for that language. A handful instead fill a genuine
**polyglot slot** — a single repository where two languages
interoperate directly, not just sit in separate directories of the
same tree:

- **deno** — a Rust core with a TypeScript/JavaScript runtime layer
  where Rust "ops" are called directly from the JS/TS bootstrap.
- **gradle** — a Java core with Kotlin and Groovy DSL layers, where
  the same build logic is invocable from either DSL against the same
  Java-implemented APIs.
- **firebase-ios-sdk** — a Swift API layer sitting directly on top of
  vendored C++ cores, bridged through Objective-C++.
- **cpython** — the interpreter's built-in types, import system, and
  large parts of the standard library implemented in C and called
  directly from Python, with the reverse direction (C code invoking
  Python callables) throughout.

These exist specifically to stress-test cross-language symbol
resolution and dependency-graph extraction — a real interop boundary,
not incidental multi-language directories that happen to sit next to
each other.

## Real, licensed, and stable

Every golden repo is a real, actively-maintained open-source project
under a permissive or copyleft license workable for an internal,
non-redistributed eval fixture (MIT, Apache-2.0, GPL-3.0, PSF-2.0 —
see `evaluation/reference/golden-repo-list` for which license applies
to which repo) with deep git history, since several query categories
(`temporal`, `co_change`, `graph_at_time`) specifically need real
commit history to test against. A synthetic or toy repo couldn't
exercise those paths meaningfully.
