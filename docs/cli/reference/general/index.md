---
title: "index"
domain: cli
category: reference
tldr: "contextual index [path] extracts your repository's blame history and dependency graph, then chunks and embeds it into the local LanceDB store — use --incremental for git-diff-only updates, --force to rebuild fully."
order: 2
---

<Callout variant="tldr">
`contextual index [path]` reads your repository and populates the local
database: git blame and dependency-graph extraction run first, then
chunking and embedding. Use `--incremental` for fast, diff-only updates
after the first run; use `--force` to rebuild everything from scratch.
</Callout>

## Usage

```
contextual index [path] [--incremental] [--force] [--bootstrap-cochange]
```

- `path` (optional, positional) — path to a repository or file to index.
  Defaults to `.`.
- `--incremental`, `-i` — only process files git reports as changed,
  instead of a full repository walk. Fast, safe for regular use after
  the first index.
- `--force`, `-f` — force a full re-index even if the workspace looks
  up to date.
- `--bootstrap-cochange` — walk up to 2,000 git commits to pre-populate
  co-change data (used by `co_change_analysis`). Only takes effect
  together with `--force`. Commits touching more than 50 files are
  excluded, since a sweep-style commit doesn't reflect real coupling.

<Terminal lines={[
  {command: "contextual index --incremental"},
  {output: "Indexing 6 changed files...\nDone. 6 files, 41 chunks updated.", muted: true}
]} />

<Callout variant="note">
The first index on a large repository is the slowest thing you'll do
with Contextual — it's several sequential stages (blame history, then
graph extraction, then chunking and embedding — graph extraction runs
*before* embedding, not after), and the embedding step runs entirely on
CPU, no GPU dependency, no network call. Every run after that can be
`--incremental` and only touches what git says actually changed.
</Callout>

## See also

- `indexing/explanation/how-indexing-works` — what each stage actually does.
- `indexing/how-to/speed-up-or-debug-a-slow-index-run`.
- `cli/how-to/force-a-full-re-index-after-a-large-refactor`.
