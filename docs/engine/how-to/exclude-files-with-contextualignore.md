---
title: Exclude Files from Indexing with .contextualignore
domain: engine
category: how-to
tldr: Add a .contextualignore file at your repo root using the same syntax as .gitignore — patterns in either file are honored, and excluding a path from one doesn't require repeating it in the other.
order: 5
related:
  - engine/reference/contextualignore-reference.md
  - engine/how-to/speed-up-or-debug-a-slow-index-run.md
---

<Callout variant="tldr">
`.contextualignore` uses plain `.gitignore` syntax. It's additive with
`.gitignore`, not a replacement — a path excluded by either file is
excluded from indexing, so you only need `.contextualignore` for things
you want excluded from Contextual specifically but not from git.
</Callout>

## Quick start

Create `.contextualignore` at your repository root:

<Terminal lines={[
  {command: "cat .contextualignore"},
  {output: "# gitignore syntax\nnode_modules/\ndist/\n*.min.js\n*_pb2.py\ngenerated/", muted: true}
]} />

No installation or registration step — Contextual reads this file
automatically on the next index.

## Why you'd add something here that isn't already in `.gitignore`

`.gitignore` controls what git tracks; `.contextualignore` controls what
Contextual indexes. Most of the time these overlap completely — anything
git ignores, you also don't want indexed. Reach for `.contextualignore`
specifically when you want a file *tracked in git* but *not indexed* —
committed fixtures full of noisy generated content, a large committed
dataset, vendored code you keep in version control but don't want
cluttering search results.

## What's excluded by default

Contextual ships default patterns covering common cases you shouldn't
need to redeclare yourself: dependency directories (`node_modules/`,
`.venv/`, `vendor/`), build output (`dist/`, `build/`, `target/`),
minified and generated files (`*.min.js`, `*_pb2.py`, `*.d.ts`), lockfiles,
database files, and IDE directories. Add `.contextualignore` when your
project has *additional* noise beyond these defaults — you're extending
the exclusion list, not starting from zero.

## Syntax notes

Same rules as `.gitignore`: one pattern per line, `#` starts a comment,
blank lines are skipped, a trailing `/` matches directories only, and
in-file negation (`!pattern`) works the same way it does in a
`.gitignore`. See `engine/reference/contextualignore-reference` for the
complete syntax and precedence rules.

## After adding or changing it

Excluding previously-indexed files doesn't retroactively remove them from
the database — run a fresh index (`--force` for a full rebuild, or just
let the next `--incremental` run naturally stop re-indexing the excluded
paths) to actually apply the change. If you're excluding something
specifically because it was bloating a slow index run, see
`engine/how-to/speed-up-or-debug-a-slow-index-run`.
