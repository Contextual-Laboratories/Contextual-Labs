---
title: "doctor"
domain: cli
category: reference
tldr: "contextual doctor runs seven independent health checks (Configuration, Directories, Models, Daemon & Locks, Database, MCP Integration, Git Integration) and prints one pass/fail line with detail per check."
order: 3
---

<Callout variant="tldr">
`contextual doctor` runs seven independent checks and prints a
pass/fail line with a specific detail message for each — not one overall
health score. See `engine/observability/interpreting-doctor-report` for
what each check actually means and how to act on a failure.
</Callout>

## Usage

```
contextual doctor
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           OK    Embed weights present.\nDaemon & Locks   OK\nDatabase         OK\nMCP Integration  OK    clients.json exists.\nGit Integration  OK", muted: true}
]} />

## See also

- `engine/observability/interpreting-doctor-report` — full detail on
  every check.
