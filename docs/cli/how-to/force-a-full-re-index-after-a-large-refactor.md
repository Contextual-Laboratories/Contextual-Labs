---
title: Force a full re-index after a large refactor
domain: cli
category: how-to
tldr: Run contextual index --force to rebuild the entire index instead of relying on the incremental, git-diff-only path.
order: 2
---

<Callout variant="tldr">
`contextual index --force` rebuilds the full index instead of only
processing git-diff changes — the right move after a large rename/move
sweep where incremental indexing might miss coupling context.
</Callout>

```
contextual index --force
```

<Terminal lines={[
  {command: "contextual index --force"},
  {output: "Force re-indexing entire repository...\nDone. 342 files, 4,108 chunks.", muted: true}
]} />

<Callout variant="note">
Add `--bootstrap-cochange` together with `--force` if you also want
co-change data rebuilt from git history (up to 2,000 commits, excluding
sweep-style commits touching more than 50 files) — this only takes
effect alongside a forced full rebuild, not on its own.
</Callout>

## See also

- `cli/reference/general/index`.
- `indexing/explanation/how-indexing-works`.
