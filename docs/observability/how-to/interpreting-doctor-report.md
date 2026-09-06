---
title: Interpreting a doctor Report
domain: observability
category: how-to
tldr: "contextual doctor runs nine independent checks — Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, Git Integration, Indexing Job, and Index Freshness — each pass/fail with a specific detail line, not a single overall health score."
order: 1
---

<Callout variant="tldr">
`contextual doctor` runs nine independent checks and prints one
pass/fail line per check, each with a specific detail message — not a
single "healthy/unhealthy" verdict. Read the detail line, not just the
pass/fail, since that's where the actual next step lives.
</Callout>

```
contextual doctor
```

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nMCP Integration  OK    clients.json exists.\nGit Integration  OK\nIndexing Job     OK    Last run: succeeded.\nIndex Freshness  OK    Up to date — no changes since the last index.", muted: true}
]} />

## The nine checks

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

**Indexing Job** — whether this workspace's last background indexing job
(see `cli/reference/general/index`) actually finished cleanly. A job
record showing "running" is checked for a real live process, not just
trusted at face value — if the process that owned it is gone (crashed,
`kill -9`, power loss) this check fails with the stage it was on and
tells you to run `contextual index` again to resume, rather than reading
as healthy indefinitely.

**Index Freshness** — informational, not a pass/fail gate: how many
files have changed (by git status) since the last completed index. A
workspace mid-edit is expected to show unindexed changes; this only ever
fails if the freshness check itself errors. See
`indexing/explanation/incremental-vs-scheduled-indexing` for when your
index actually catches back up.

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
