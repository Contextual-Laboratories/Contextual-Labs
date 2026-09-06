---
title: "doctor"
domain: cli
category: reference
tldr: "contextual doctor runs nine independent health checks (Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, Git Integration, Indexing Job, Index Freshness) and prints one pass/fail line with detail per check."
order: 3
---

<Callout variant="tldr">
`contextual doctor` runs nine independent checks and prints a
pass/fail line with a specific detail message for each — not one overall
health score. See `observability/how-to/interpreting-doctor-report` for
what each check actually means and how to act on a failure.
</Callout>

## Usage

```
contextual doctor
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nMCP Integration  OK    clients.json exists.\nGit Integration  OK\nIndexing Job     OK    Last run: succeeded.\nIndex Freshness  OK    Up to date — no changes since the last index.", muted: true}
]} />

## See also

- `observability/how-to/interpreting-doctor-report` — full detail on
  every check.
