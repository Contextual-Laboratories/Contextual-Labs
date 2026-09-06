---
title: Speed Up or Debug a Slow Index Run
domain: indexing
category: how-to
tldr: Use --incremental instead of a full re-index whenever you can, check .contextualignore for files you don't actually need indexed, and read the stage names in `contextual index --status` to see whether Dynamic Index or Deep Index is actually the slow one.
order: 2
related:
  - indexing/explanation/how-indexing-works.md
  - indexing/how-to/size-your-machine-before-indexing-a-large-repo.md
  - indexing/reference/contextualignore-reference.md
---

<Callout variant="tldr">
Most "indexing is slow" cases are one of three things: you're running a
full index when `--incremental` would do, you're indexing files that
should be excluded, or one specific pipeline stage (not the whole run) is
the actual bottleneck. Check which before assuming something's broken.
</Callout>

## First: are you running the right kind of index?

`contextual index --force` re-embeds and re-extracts everything,
regardless of what's changed — this is the slow one, and it's supposed to
be. Day-to-day, `contextual index --incremental` only processes files git
reports as changed, which is fast by design. If a run feels slow, check
which one you actually ran first.

<Terminal lines={[
  {command: "contextual index --incremental"},
  {output: "Indexing 6 changed files...\nDone. 6 files, 41 chunks updated.", muted: true}
]} />

`--bootstrap-cochange` is its own separate cost — it walks up to 2,000
past commits to seed file-coupling data for `co_change_analysis`, and
only does anything when combined with `--force`. Don't reach for it on
every full re-index; it's a one-time seed, not something you need to
repeat regularly.

## Second: are you indexing files you don't need?

Every file discovered gets chunked and embedded, so a large
`node_modules`, a vendored dependency tree, or generated/build output
sitting inside your repo directly inflates index time for zero retrieval
value. Check `.contextualignore` — see
`indexing/reference/contextualignore-reference` for syntax — and confirm
the files actually bloating your run are covered by a pattern there or in
`.gitignore` (both are honored).

## Third: figure out which tier is actually slow

A full index runs as a background job in two tiers — Dynamic Index
(discovery, blame, graph extraction, keyword search) then Deep Index
(chunk + embed, backfill, search-index rebuild) — see
`indexing/explanation/how-indexing-works` for what each one does. Run
`contextual index --status` to see which tier is currently running and
how far along it is, rather than guessing from wall-clock time alone:

- Slow during **Dynamic Index** (blame/graph extraction): usually a very
  deep git history, or a huge number of files needing fresh blame (no
  cache hit yet) — this should get faster on the next run once the blame
  cache is warm. Graph extraction cost is proportional to file count and
  language mix — languages with full structural resolution (see
  `indexing/reference/language-support-matrix`) cost more here than
  chunk-only languages. Graph/blame/keyword search are already usable
  once this tier finishes, even if Deep Index is still running.
- Slow during **Deep Index** (chunk + embed): this is the CPU-bound
  tier — see `indexing/how-to/size-your-machine-before-indexing-a-large-repo`
  for what actually drives its cost. Embedding uses every available core
  by default; if the machine is also doing something else CPU-heavy at
  the same time, this is the tier that'll feel it. Until it finishes,
  `search`/`nexus_search` transparently fall back to Dynamic Index's
  keyword layer rather than returning incomplete semantic results — see
  `retrieval/how-to/understand-stale-or-missing-results`.

<Callout variant="note">
Within Dynamic Index, stage order is not "chunk, then embed, then extract
the graph" — graph and blame extraction run *before* Deep Index's
chunking and embedding. If you're timing stages against an assumption
about ordering, check `indexing/explanation/how-indexing-works` first;
the actual order is specifically the opposite of what you'd guess.
</Callout>

## If it's still unexpectedly slow

Run `contextual doctor` first — see
`observability/how-to/interpreting-doctor-report` — to rule out an
unhealthy daemon, a missing model download, or a crashed indexing job
(the Indexing Job check) inflating what looks like a slow run. If
everything there checks out and one tier is still disproportionately
slow relative to your repository's size, that's worth reporting with the
tier name (from `contextual index --status`) and rough file/repo size,
since "slow" without a tier name is hard to act on.
