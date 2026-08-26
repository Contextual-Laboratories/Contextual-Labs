---
title: Interpreting a doctor Report
domain: observability
category: how-to
tldr: "contextual doctor runs seven independent checks — Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, and Git Integration — each pass/fail with a specific detail line, not a single overall health score."
order: 1
---

<Callout variant="tldr">
`contextual doctor` runs seven independent checks and prints one
pass/fail line per check, each with a specific detail message — not a
single "healthy/unhealthy" verdict. Read the detail line, not just the
pass/fail, since that's where the actual next step lives.
</Callout>

```
contextual doctor
```

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nMCP Integration  OK    clients.json exists.\nGit Integration  OK", muted: true}
]} />

## The seven checks

**Configuration** — whether a global config file exists, and if a
workspace-level config also exists, whether the two are consistent.
A failure here almost always means `contextual init` was never run, or
was interrupted partway.

**Directories** — whether the expected on-disk layout under
`.contextual/` is intact. A failure means something outside Contextual
deleted or corrupted part of the workspace directory structure.

**Models** — whether the local embedding model weights are present on
disk. If this fails, the detail line tells you directly: "Embed weights
missing. Run `contextual fetch`." — that's the actual fix, not a
reinstall.

**Daemon & Locks** — whether the background daemon's lockfile is
consistent with what's actually running: a real, live process on the
recorded PID, actually listening on the recorded port. A failure here is
the "MCP daemon acting up" case — see
`troubleshooting/daemon-not-responding` for what to do about it
specifically.

**Database** — whether the LanceDB store exists both globally and for
the current workspace. A failure typically means `contextual index` has
never been run in this repository, or the workspace's database directory
was deleted outside of Contextual.

**MCP Integration** — whether `clients.json` (the record of which AI
clients are configured for this workspace) exists and passes an
integrity check. If the file exists but fails integrity, the detail line
says so explicitly ("integrity check failed (tampered)") rather than
silently treating it as absent.

**Git Integration** — whether the current directory is inside a git
repository. Contextual's temporal features (blame, history, decisions)
depend on this; a failure here doesn't stop indexing, but it does mean
temporal/blame-enriched features won't have anything to draw on.

<Callout variant="note">
Each check's detail message is written to tell you what to run next, not
just what's wrong — "Embed weights missing. Run `contextual fetch`." is
the pattern to expect throughout, not a generic error string.
</Callout>

## Where the underlying logs live

If a `doctor` check fails and its detail line isn't enough to act on,
the full daemon and CLI logs are under `~/.local/state/contextual/logs/`.
This is the same path the CLI itself points you to when a command fails
outright (for example, a failed `contextual init`).
