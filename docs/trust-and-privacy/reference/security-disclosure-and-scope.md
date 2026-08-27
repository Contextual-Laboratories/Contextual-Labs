---
title: Security disclosure and scope
domain: trust-and-privacy
category: reference
tldr: "What's in scope for a security review, and how to report a finding."
related:
  - trust-and-privacy/reference/data-privacy.md
  - trust-and-privacy/explanation/how-the-license-and-auth-model-works.md
---

<Callout variant="tldr">
Report a security finding to **<team@contextuallabs.dev>** with the
subject line `[SECURITY] Contextual` — not a public GitHub issue. This
page covers what's realistically in scope for a local-first daemon
like Contextual, and a few limitations that are known and accepted
rather than open bugs.
</Callout>

## In scope

- **The daemon's local trust boundary** — loopback enforcement, bearer
  token handling, and the platform-specific mechanisms that back it
  (Unix domain socket permissions on macOS/Linux, ACL-hardened token
  file and port-ownership verification on Windows).
- **Workspace isolation** — anything that lets one workspace's tool
  calls read or affect another workspace's data.
- **Input handling on the MCP tool surface** — SQL/filter injection
  into LanceDB queries, path traversal, or any way a crafted tool
  argument reaches somewhere it shouldn't.
- **Authentication and licensing** — the OAuth login flow, token
  storage and revocation, and the license-activation backend. See
  `trust-and-privacy/explanation/how-the-license-and-auth-model-works`
  for how that flow is supposed to behave.
- **Rate limiting** — ways to bypass or exhaust the daemon's per-client
  request limits.

<Callout variant="warning">
Do not test any of the above against another person's account,
license, or machine. Findings here should be demonstrated against your
own local install.
</Callout>

## Out of scope

- **Findings about the contents of your own indexed codebase.**
  Contextual surfaces what's already on disk; a secret already
  committed to a repo you indexed is a problem with that repo, not
  with Contextual.
- **The AI client you're using Contextual through** (Claude Code,
  Cursor, Windsurf, etc.) — report client-specific issues to that
  client's own project.
- **Denial of service against your own local daemon.** Since the
  daemon only ever serves your own machine, availability issues there
  don't have the same impact as they would on a shared service — still
  worth reporting if you find one, just not treated as high severity.

## Known limitations (accepted, not open bugs)

A few properties of the current security model are deliberate
trade-offs for a local-first, single-user tool rather than gaps
waiting to be closed. Reporting these isn't necessary, though real
exploitability beyond what's described here is:

- **No TLS between the shim and the daemon.** Traffic between the MCP
  shim process and the daemon runs over loopback HTTP, unencrypted.
  On a normal single-user workstation this doesn't cross a network
  boundary at all; if you're running in an environment with
  process-level snooping between users (a shared VM or container),
  treat that as a factor in your own isolation, not something
  Contextual's loopback boundary is meant to defend against.
- **Single-user by design.** The authentication model assumes one
  user per machine. Multi-user deployment on a shared host isn't a
  supported configuration.
- **Auth failures aren't persisted to the audit log.** Successful tool
  calls are recorded to the local audit trail; rejected/unauthenticated
  requests are logged to the daemon's own log file instead, not to
  that table.
- **Invalid `config.toml` values fall back to defaults silently**
  rather than failing the command outright.

## How to report

Email **<team@contextuallabs.dev>** with the subject line
`[SECURITY] Contextual`. Include a description of the issue,
reproduction steps, and your `contextual --version` output. Please
don't open a public GitHub issue for anything security-related — use
the email address above so a fix can go out before the details are
public.
