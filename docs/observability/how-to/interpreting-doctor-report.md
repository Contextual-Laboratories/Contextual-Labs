---
title: Interpreting a doctor Report
domain: observability
category: how-to
tldr: "contextual doctor runs independent checks — nine always shown (Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, Git Integration, Index Freshness, License) and four that appear only when relevant (Index Content, Degraded State, Daemon Activity, Indexing Job) — each pass/fail with a specific detail line, not a single overall health score."
order: 1
---

<Callout variant="tldr">
`contextual doctor` runs independent checks and prints one pass/fail
line per check, each with a specific detail message — not a single
"healthy/unhealthy" verdict. Some rows only appear when there is
something to report. Read the detail line, not just the
pass/fail, since that's where the actual next step lives.
</Callout>

```
contextual doctor
```

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nIndex Content    OK    4210 code chunk(s), 312 doc chunk(s) indexed.\nMCP Integration  OK    clients.json exists and integrity verified.\nDegraded State   OK    No self-reported issues.\nGit Integration  OK\nIndex Freshness  OK    Up to date — no changes since the last index.\nLicense          OK    Trial active — 9 days left.", muted: true}
]} />

## The checks

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

**Index Content** — shown when the workspace has a database. Counts
the rows actually in the code and doc chunk tables, which the Database
check above can't tell you: a database directory exists the moment
`contextual init` runs, before anything is indexed. A failure here means
no code chunks were found, so run `contextual index .`.

**Degraded State** — shown when the daemon is running. Reports anything
the daemon has noticed and flagged about itself (a tamper flag, a
workspace whose startup reconciliation gave up, a retention prune that
keeps failing) instead of leaving it as a line in a log file. With
nothing to report it says so; with something to report you get one
`Degraded: <category>` row per issue, with how many times it was seen.

**Daemon Activity** — shown only when there is background work to
explain: the daemon is indexing file changes ("Incrementally indexing 2
workspace(s) right now."), a bulk index is running for this workspace
(with its stage and percent), a bulk job holds the machine-wide model
lease, or background work was deferred while a bulk job owned a workspace
("12 unit(s) of background work were deferred … they resume on their own").
The daemon deliberately steps aside for a bulk index: its startup
reconciliation, periodic maintenance, git-hook indexing and live file events
all wait until the job is done, then catch up. An idle daemon prints no row
at all. This is for answering "why is my machine busy" or "why is that result
a moment behind," not a health signal.

**Retention** — appears only when something is wrong: it reports a
telemetry or audit table that holds rows older than twice its retention
window, meaning the background pruner is not keeping up. It is computed from
the table's contents, not from a failure counter, so it stays visible across
daemon restarts. A value of `-1` for a table means it could not be measured.
See `observability/reference/logs-and-retention-reference`.

**Indexing Job** — shown only when there is something to say. While a
background indexing job is running it shows the current stage. If the
last job failed it shows the error. A job record that says "running" is
checked for a real live process, not just trusted: if the process that
owned it is gone (crashed, `kill -9`, power loss) this check fails with
the stage it was on and tells you to run `contextual index` again to
resume. A job that finished cleanly, was canceled, or never existed
prints no row, because there is nothing to act on. See
`cli/reference/general/index`.

**Index Freshness** — informational, not a pass/fail gate: how many
files have changed (by git status) since the last completed index. A
workspace mid-edit is expected to show unindexed changes; this only ever
fails if the freshness check itself errors. See
`indexing/explanation/incremental-vs-scheduled-indexing` for when your
index actually catches back up.

**License** — reads your local license state only; no network call on
the common path. Reports which phase you're in (trial running, active
paid subscription, in grace after a lapse, or hard-expired) with days
remaining where relevant. If it finds a stale or missing license file
alongside a still-valid login session, it makes one bounded,
cooldown-limited repair attempt before reporting failure, so a routine
`doctor` run can often self-heal a split state rather than just
surfacing it. See `account/explanation/how-licensing-works-here` for
what each phase means for what still works.

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
