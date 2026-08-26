---
title: Config precedence (global vs. per-workspace)
domain: configuration
category: explanation
tldr: Every workspace has its own config.toml layered on top of a global config — workspace settings are what most commands actually read, while the global config covers machine-wide defaults.
order: 1
---

<Callout variant="tldr">
Contextual has two config layers: one global file (`~/.contextual/`-ish
machine-wide defaults) and one `config.toml` per workspace, inside that
workspace's `.contextual/` directory. Most day-to-day settings live at
the workspace level.
</Callout>

## Two layers, two purposes

The **global config** holds machine-wide state — think of it as "how
this machine's Contextual installation behaves by default." The
**workspace config** (`.contextual/config.toml` inside a specific
repository) holds that workspace's own indexing, retrieval, and cache
settings, plus its identity (workspace ID, root path, creation
timestamp).

`contextual config` opens the workspace config by default; pass
`--global` to open the global one instead. Same for `contextual config
reset`.

## Why workspace identity is protected on reset

A workspace's `workspace_id` and `created_at` are never reset along
with its tunable settings — `contextual config reset` explicitly
preserves them. This matters because your indexed data in the database
is tied to that workspace ID; re-minting it on every reset would
silently orphan everything you'd already indexed, disconnecting your
config from your own data.

## See also

- `cli/reference/config/config`, `cli/reference/config/config-reset`.
- `configuration/how-to/reset-config-to-defaults`.
