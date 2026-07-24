---
title: "MCP daemon not responding — what's actually happening"
domain: troubleshooting
category: troubleshooting
tldr: "\"Not responding\" is usually one of three distinct things: the daemon is still warming up (model load), a competing process is fighting over the spawn lock, or a stale process is alive but never answers HTTP and gets auto-killed and replaced."
order: 3
related:
  - troubleshooting/workspace-not-connecting.md
  - mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload.md
  - observability/how-to/read-and-interpret-daemon-and-cli-logs.md
---

<Callout variant="tldr">
When the daemon seems unresponsive, it's rarely a mystery hang — it's
one of three specific, self-describing situations: still starting up
(model warmup can take tens of seconds), a spawn race with another
client trying to start it at the same moment, or a genuinely stuck
process that Contextual detects and replaces on its own.
</Callout>

## Case 1 — it's still starting up

Model warmup alone can take tens of seconds. If you just ran
`contextual mcp start`, added a new client, or the daemon hadn't run
recently, "not responding" for the first stretch is expected, not
broken. Contextual's own startup logic waits on the daemon's `/live`
endpoint up to a configurable startup timeout before treating it as a
real failure — worth giving it that same benefit of the doubt yourself
before escalating.

<Terminal lines={[
  {command: "contextual mcp logs --follow"},
  {output: "[daemon] Starting MCP server...\n[daemon] Warming up embedding model...\n[daemon] Ready.", muted: true}
]} />

## Case 2 — "daemon failed to start within timeout"

If you see this exact message, one of two things happened: the daemon
process started but never answered its health check before the timeout
expired, or (if you see the variant "waited for peer spawner") another
client was already in the middle of spawning the daemon and that attempt
itself never finished in time. Both are genuine failures worth
investigating, not routine — check `~/.contextual/daemon.log` (via
`contextual mcp logs`) for what the daemon process itself printed right
before the timeout; a Python traceback there is the real root cause, the
timeout message is just the symptom.

## Case 3 — a stale process gets auto-detected and replaced

Contextual actively guards against exactly this: if a daemon process is
alive but never starts answering HTTP requests, it's treated as stale,
terminated, and a fresh one is spawned in its place — automatically, on
your next command. If you notice the daemon's PID changed between two
commands without you restarting it yourself, this self-healing is almost
certainly why — check `daemon.log` around that timestamp for
`daemon_unresponsive_killing_stale` to confirm.

## Case 4 — a concurrent spawn race

Only one client is allowed to actually spawn the daemon at a time — a
spawn-lock file enforces this so two AI clients connecting at the same
moment don't race into starting two competing daemon processes. If
you're opening multiple clients simultaneously against a workspace for
the first time, a brief "waiting for peer spawner" delay is expected
behavior, not a bug.

## If none of these explain it

Run `contextual doctor` and check the **Daemon & Locks** line
specifically — see `observability/how-to/interpreting-doctor-report`. If
the daemon process is confirmed not running at all and won't start even
after `contextual mcp restart`, check `contextual mcp logs` for the
actual startup exception rather than retrying blindly — every daemon
startup failure logs the real underlying error before the generic
timeout message surfaces to the CLI.
