---
title: "hooks-install"
domain: cli
category: reference
tldr: "contextual hooks-install [path] wires post-commit, post-checkout, and post-merge git hooks that trigger an incremental index automatically — no path needed if you're already inside the repo."
order: 25
---

<Callout variant="tldr">
`contextual hooks-install` adds three git hooks (post-commit,
post-checkout, post-merge) that trigger an incremental re-index
automatically, so your index stays current without you remembering to
run `contextual index` by hand.
</Callout>

## Usage

```
contextual hooks-install [path]
```

- `path` (optional, positional) — workspace root to install into.
  Defaults to the current git repository.

<Terminal lines={[
  {command: "contextual hooks-install"},
  {output: "✓ post-commit  installed\n✓ post-checkout  installed\n✓ post-merge  installed\nHooks installed\n.git/hooks · auto-indexes on commit, checkout, merge", muted: true}
]} />

## See also

- `cli/reference/general/hooks-uninstall`.
- `cli/reference/general/index`.
