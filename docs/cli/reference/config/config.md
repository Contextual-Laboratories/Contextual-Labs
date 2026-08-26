---
title: "config"
domain: cli
category: reference
tldr: "contextual config [--global] opens the workspace (or global, with --global) config.toml in your default editor, creating a default one first if none exists."
order: 19
---

<Callout variant="tldr">
`contextual config` opens the current workspace's `config.toml` in your
default editor. Pass `--global` to open the global config instead. If a
workspace config doesn't exist yet, one is created with defaults first.
</Callout>

## Usage

```
contextual config [--global]
```

- `--global`, `-g` — open the global config file instead of the current
  workspace's config.

<Terminal lines={[
  {command: "contextual config"},
  {output: "Opening /Users/you/dev/api-core/.contextual/config.toml in $EDITOR...", muted: true}
]} />

## See also

- `cli/reference/config/config-reset`.
- `configuration/explanation/config-precedence` for how global and workspace
  config interact.
