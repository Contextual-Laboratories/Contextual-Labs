---
title: "hooks-uninstall"
domain: cli
category: reference
tldr: "contextual hooks-uninstall [path] removes only the sections of git hooks that Contextual installed — any third-party hook content in the same files is preserved."
order: 26
---

<Callout variant="tldr">
`contextual hooks-uninstall` removes Contextual's own git hooks. If a
hook file also contains other tools' hook content, only Contextual's
section is stripped out — the rest is left alone.
</Callout>

## Usage

```
contextual hooks-uninstall [path]
```

- `path` (optional, positional) — workspace root. Defaults to the
  current git repository.

<Terminal lines={[
  {command: "contextual hooks-uninstall"},
  {output: "✓ post-commit  stripped\n✓ post-checkout  stripped\n✓ post-merge  stripped\nHooks removed\n.git/hooks", muted: true}
]} />

## See also

- `cli/reference/general/hooks-install`.
