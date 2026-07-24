---
title: Platform Support & Known Limitations
domain: indexing
category: reference
tldr: macOS, Linux, and Windows are all tested in CI across Python 3.11-3.14. Windows' daemon trust boundary works differently from macOS/Linux by necessity, and while the gap this used to leave has real code-level fixes now, they haven't yet been validated on real Windows hardware.
order: 8
related:
  - trust-and-privacy/reference/data-privacy.md
  - mcp/server/explanation/mcp-daemon-lifecycle-and-hot-reload.md
---

<Callout variant="tldr">
Contextual runs on macOS, Linux, and Windows, all tested in CI. The one
platform-specific nuance worth knowing: Windows' local daemon trust
boundary works differently from macOS/Linux, for real OS-level reasons —
that gap has real fixes now, exercised by tests, but not yet confirmed on
physical Windows hardware.
</Callout>

## Supported platforms

macOS, Linux, and Windows are all in the CI test matrix, across Python
3.11 through 3.14. There's no reduced feature set on any platform for
indexing, retrieval, or the graph — the differences described below are
specific to how the local MCP daemon secures itself, not to what
Contextual can do for your codebase.

## The Windows daemon trust boundary, explained honestly

On macOS and Linux, the daemon communicates over a Unix domain socket
inside a directory with owner-only (0700) permissions — the operating
system itself guarantees only your own user account can even reach the
socket, before any application-level check runs. Windows has no direct
equivalent to a Unix domain socket for this purpose, so the daemon binds
a local TCP port instead, and a request still needs a valid bearer token
either way (the daemon rejects unauthenticated or non-loopback
connections on both platforms).

The gap this created was specific: without an OS-level filesystem
boundary, a different local process could theoretically race to bind
that port before the real daemon started, and receive the bearer token
when the client unwittingly connected to it instead of the real daemon —
a local, not remote, confused-deputy scenario, and one that requires
another process already running on your machine.

<Callout variant="note">
This has real, shipped mitigations now: Windows-specific ACL hardening on
the token file and lock files (restricting them to your user account,
where a plain permission-bit change is a no-op on Windows), plus a
startup-time check that verifies the process actually listening on the
daemon's port is the one Contextual itself spawned before trusting it.
Both are covered by a dedicated test suite. What hasn't happened yet is
manual verification on physical Windows hardware — the fix has been
validated via tests that simulate Windows-specific behavior, not (yet)
by running it on a real Windows machine. Treat this as substantially
improved, not yet fully field-verified.
</Callout>

## What this means practically

This is a local-attacker scenario requiring code already running on your
machine — it was never remotely exploitable, and if a machine is already
compromised to that degree, this specific gap was rarely an attacker's
best option to begin with. It's worth knowing about if you're evaluating
Contextual for a shared or multi-user Windows machine specifically, less
relevant on a single-user workstation.

## Everything else

Outside of this one daemon-security nuance, macOS/Linux/Windows behavior
is uniform: the same indexing pipeline, the same retrieval and graph
behavior, the same CLI and MCP tool surface. See
`trust-and-privacy/reference/data-privacy` for the full local trust-boundary
picture beyond this Windows-specific detail.
