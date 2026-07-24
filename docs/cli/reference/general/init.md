---
title: "init"
domain: cli
category: reference
tldr: "contextual init [path] creates a .contextual/ workspace in a repository — config, database, and gitignore entries, no source files touched."
order: 1
---

<Callout variant="tldr">
`contextual init [path]` sets up a `.contextual/` workspace in the given
repository (default: current directory). It writes config and the local
database directory, and adds the right entries to `.gitignore` — it does
not read or modify any of your own source files.
</Callout>

## Usage

```
contextual init [path]
```

- `path` (optional, positional) — repository root to initialize.
  Defaults to `.`.

<Terminal lines={[
  {command: "contextual init"},
  {output: "Creating .contextual/ workspace...\nWorkspace ready.", muted: true}
]} />

## What it does

Creates the `.contextual/` directory for this workspace, writes a
default `config.toml`, and adds `.contextual/` to your repository's
`.gitignore` automatically — a workspace is never meant to be committed.
This step doesn't index anything yet; run `contextual index` next.

## Failure behavior

If a step fails partway, the error message tells you whether it's safe
to just run `contextual init` again (a recoverable step) or whether you
need to check `~/.local/state/contextual/logs/` for what actually went
wrong first.

<Callout variant="warning">
If something about an existing workspace looks broken, don't
`rm -rf .contextual/` and re-run `init` as a first move — run
`contextual doctor` first (see `observability/how-to/interpreting-doctor-
report`) to see what's actually wrong. Most problems are a stale index
or config mismatch, not something a full wipe is the right fix for.
</Callout>

## See also

- `cli/reference/general/index` — the next step after `init`.
- `cli/reference/general/doctor` — check workspace health at any time.
