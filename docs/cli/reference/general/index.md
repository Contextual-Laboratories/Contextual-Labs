---
title: "index"
domain: cli
category: reference
tldr: "contextual index [path] spawns a background indexing job and shows live progress — a fast Dynamic Index (graph, blame, keyword search) commits first, then Deep Index (chunking + embedding) fills in semantic search. Use --status to check on it, --cancel to stop it, --incremental for git-diff-only updates, --force to rebuild fully."
order: 2
---

<Callout variant="tldr">
`contextual index [path]` spawns a detached background job and attaches
with live progress. A fast **Dynamic Index** tier (blame, dependency
graph, keyword search) commits first — graph/blame tools and keyword
search work immediately after — then **Deep Index** (chunking +
embedding) runs in the background to fill in semantic search. Ctrl+C or
closing the terminal only stops watching, not the job itself; reattach
with `contextual index` or `contextual index --status`.
</Callout>

## Usage

```
contextual index [path] [--incremental] [--force] [--bootstrap-cochange]
contextual index --status
contextual index --cancel
```

- `path` (optional, positional) — path to a repository or file to index.
  Defaults to `.`.
- `--incremental`, `-i` — only process files git reports as changed,
  instead of a full repository walk. Fast, safe for regular use after
  the first index — typically finishes in seconds without needing the
  Dynamic/Deep Index split at all.
- `--force`, `-f` — force a full re-index even if the workspace looks
  up to date. This is what runs the full Dynamic Index → Deep Index job.
- `--bootstrap-cochange` — walk up to 2,000 git commits to pre-populate
  co-change data (used by `co_change_analysis`). Only takes effect
  together with `--force`. Commits touching more than 50 files are
  excluded, since a sweep-style commit doesn't reflect real coupling.
- `--status` — print this workspace's current indexing job status and
  exit. Never starts a new run; prints a friendly message if nothing is
  currently running.
- `--cancel` — cancel this workspace's currently running indexing job, if
  any. Never blocks waiting for it to actually stop — run `--status` (or
  just `contextual index` again) to see the eventual outcome.

<Terminal lines={[
  {command: "contextual index"},
  {output: "Indexing started…\n  extracting graph + blame + keyword search…\n  extracting symbols + embedding…\nDone. 412 files, 3,108 chunks.", muted: true}
]} />

<Callout variant="note">
The job survives you leaving it: closing the terminal or Ctrl+C on
`contextual index` stops *watching*, not the job — it keeps running
detached. If the job process itself is killed (crash, `kill -9`, power
loss), the next `contextual index` or `contextual doctor` detects it and
either resumes or reports the failure, rather than reading as healthy
indefinitely. The embedding step runs entirely on CPU — no GPU
dependency, no network call.
</Callout>

## See also

- `indexing/explanation/how-indexing-works` — what each tier actually does.
- `indexing/how-to/speed-up-or-debug-a-slow-index-run`.
- `cli/how-to/force-a-full-re-index-after-a-large-refactor`.
- `observability/how-to/interpreting-doctor-report` — the Indexing Job
  and Index Freshness checks `contextual doctor` runs against the same
  job record.
