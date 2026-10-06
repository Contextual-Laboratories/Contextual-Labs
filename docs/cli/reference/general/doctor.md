---
title: "doctor"
domain: cli
category: reference
tldr: "contextual doctor runs independent health checks (Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, Git Integration, Index Freshness, License, plus Index Content, Degraded State, Daemon Activity, and Indexing Job when they have something to report) and prints one pass/fail line with detail per check."
order: 3
---

<Callout variant="tldr">
`contextual doctor` runs independent checks and prints a pass/fail line
with a specific detail message for each — not one overall health score.
Nine rows are always shown; four more appear only when they have
something to say. See `observability/how-to/interpreting-doctor-report` for
what each check actually means and how to act on a failure.
</Callout>

## Usage

```
contextual doctor
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nIndex Content    OK    4210 code chunk(s), 312 doc chunk(s) indexed.\nMCP Integration  OK    clients.json exists and integrity verified.\nDegraded State   OK    No self-reported issues.\nGit Integration  OK\nIndex Freshness  OK    Up to date — no changes since the last index.\nLicense          OK    Trial active — 9 days left.", muted: true}
]} />

These rows are shown only when relevant, so a quiet system prints fewer
lines: **Index Content** (when the workspace has a database), **Degraded
State** (when the daemon is running), **Daemon Activity** (only while
the daemon is incrementally indexing something right now), and
**Indexing Job** (only while a job is running, or when the last one
failed or was found stalled; a clean finish or a cancel prints nothing).

The License check reads your local license state only — fully offline,
no network call on the common path. If it finds a stale or missing
license file alongside a still-valid login session, it makes one bounded,
cooldown-limited repair attempt (a short-timeout re-sync, not a full
re-activation) before reporting failure, so a routine `doctor` run can
self-heal a split state instead of just reporting it.

## See also

- `observability/how-to/interpreting-doctor-report` — full detail on
  every check.
