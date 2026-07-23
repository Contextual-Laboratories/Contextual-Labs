---
title: Incremental vs. Scheduled Indexing
domain: engine
category: explanation
tldr: Contextual re-indexes on file changes via a debounced watcher, not on a timer — there is no periodic background reindex, so a long-idle workspace only catches up once something touches it again.
order: 3
related:
  - engine/explanation/how-indexing-works.md
  - engine/how-to/understand-stale-or-missing-results.md
---

<Callout variant="tldr">
There is no scheduled or periodic full reindex anywhere in Contextual —
not weekly, not on a timer, not on daemon startup. Your index only
updates when you run `contextual index` yourself, or while the file
watcher is running and sees a change.
</Callout>

## The two ways your index actually gets updated

**A manual full index** — `contextual index` (or `contextual index
--force` to re-embed everything regardless of what's changed). This runs
the six-stage pipeline described in
`engine/explanation/how-indexing-works` once, then exits.

**A running file watcher** — started alongside the daemon, watching your
workspace for changes while it's active. Two watchers run together:

- A **debounced filesystem watcher** that reacts to file writes with a
  500ms debounce per file, and indexes up to 4 changed files concurrently
  (a bounded semaphore, not fully sequential and not unbounded).
- A **git-index watcher** that watches `.git/index` directly, with a 2
  second debounce, and picks up files you've `git add`ed but not yet
  committed — so staged-but-uncommitted changes get indexed too, not just
  changes already on disk unstaged.

Both watchers index only the files that actually changed, running graph
extraction and embedding per-file rather than as a batch pass over the
whole repository.

<Callout variant="note">
There is deliberately no scheduled or periodic full-reindex mechanism —
no cron, no weekly sweep, no timer that decides your index might be stale
and refreshes it on its own. If the watcher isn't running (daemon
stopped, or you edited files outside of any session with a live
connection), your index simply falls behind until you run `contextual
index` again or the watcher comes back up and catches the next change.
</Callout>

## What this means in practice

If you edit code in an editor or terminal session where the daemon isn't
running, or make changes while your machine is asleep, those changes sit
un-indexed until something triggers a catch-up — either you run
`contextual index --incremental` by hand, or the watcher starts back up
and reacts to the *next* change (not necessarily backfilling everything
that happened while it was down). If you suspect your index has drifted
from your working tree, running `contextual index --incremental` is
always safe and cheap — see `engine/how-to/speed-up-or-debug-a-slow-index-run`
for what makes an index run fast or slow.

This is also why a query can return a stale or missing answer even though
you know you changed the relevant file recently — see
`engine/how-to/understand-stale-or-missing-results` for how to tell the difference
between "the index hasn't caught up yet" and "the graph genuinely has
nothing to say."
