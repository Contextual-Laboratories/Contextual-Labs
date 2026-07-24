---
title: "What init and index actually do to your repo on disk"
domain: cli
category: explanation
tldr: init creates a .contextual/ workspace directory and a gitignore entry; index reads and writes into that directory only — neither command ever modifies your own source files.
order: 2
---

<Callout variant="tldr">
`init` creates `.contextual/` (config, database, state) and adds it to
your `.gitignore`. `index` reads your source files and writes derived
data — chunks, embeddings, the graph, blame metadata — into that same
directory. Neither command ever writes to, moves, or modifies any file
you'd consider your own source.
</Callout>

## What `init` writes

- `.contextual/config.toml` — this workspace's settings.
- `.contextual/` as a database directory (empty until the first
  `index` run).
- A `.gitignore` entry so the workspace itself is never accidentally
  committed.

## What `index` writes

Everything `index` produces — chunked text, vector embeddings, the
dependency graph, git-blame/temporal metadata — lands in the LanceDB
database inside `.contextual/`. It reads your repository's files and
your git history to produce this; it does not rewrite, reformat, or
touch any of them.

## What neither command does

Neither `init` nor `index` runs a linter, formatter, or any code
transformation. If a file in your repository changes after running
either command, that change came from something else — not Contextual.

For the actual stage-by-stage mechanics of indexing (blame history and
graph extraction run first, then chunking and embedding — not the order
you'd guess), see `indexing/explanation/how-indexing-works`.

## See also

- `cli/reference/general/init`, `cli/reference/general/index`.
