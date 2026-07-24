---
title: Run diagnostics when something's broken
domain: cli
category: how-to
tldr: Run contextual doctor first, before anything more drastic — it checks seven subsystems independently and tells you which one is actually failing.
order: 8
---

<Callout variant="tldr">
`contextual doctor` should be your first move when something's broken —
not a reinstall, not deleting `.contextual/`. It checks seven
independent subsystems and tells you exactly which one is failing and
what to do about it.
</Callout>

```
contextual doctor
```

<Terminal lines={[
  {command: "contextual doctor"},
  {output: "Configuration    OK\nDirectories      OK\nModels           FAIL   Embed weights missing. Run 'contextual fetch'.\nDaemon & Locks   OK\nDatabase         OK\nMCP Integration  OK\nGit Integration  OK", muted: true}
]} />

## See also

- `cli/reference/general/doctor`.
- `observability/how-to/interpreting-doctor-report` — what every check
  actually means.
