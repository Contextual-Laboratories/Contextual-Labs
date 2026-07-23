---
title: Debug a tool returning zero results
domain: mcp-server
category: how-to
tldr: First confirm the workspace/entity was actually resolved (not just silently empty), then check contextual doctor's Database and Daemon & Locks lines before assuming the tool is broken.
order: 6
---

<Callout variant="tldr">
Zero results has more than one cause: an empty-but-valid answer, an
entity that was never found, a stale/empty index, or a daemon that
isn't actually running the workspace you think it is. Check them in
that order.
</Callout>

1. **Is it genuinely zero, or was the entity not found at all?** Graph
   tools distinguish these explicitly — see
   `engine/troubleshooting/entity-not-found`.
2. **Is the workspace actually indexed?** Run `contextual doctor` and
   check the **Database** line.
3. **Is the daemon serving the workspace you expect?** Run `contextual
   mcp status` — see `engine/troubleshooting/workspace-not-connecting`
   if it looks wrong.
4. **For `search`/`nexus_search` specifically**: a very narrow or
   unusual query can legitimately return few or no results — try a
   broader phrasing before assuming something's broken.

## See also

- `mcp-server/reference/get_doctor`, `cli/reference/doctor`.
- `engine/observability/interpreting-doctor-report`.
