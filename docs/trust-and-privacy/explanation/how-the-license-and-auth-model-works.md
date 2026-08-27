---
title: How the license and auth model works
domain: trust-and-privacy
category: explanation
tldr: "OAuth 2.0 device-code login, no stored passwords, one-time activation, and what the license check touches."
related:
  - account/explanation/how-licensing-works-here.md
  - trust-and-privacy/reference/data-privacy.md
---

<Callout variant="tldr">
Logging in uses standard OAuth 2.0 — an in-browser flow with a
device-code fallback for headless environments — and no password is
ever collected or stored. Licensing is node-locked and checked
offline after a one-time activation step. Neither path ever touches
your code.
</Callout>

## Logging in: OAuth, not a password

`contextual login` uses the Authorization Code flow with PKCE, opened
in your default browser. Contextual's CLI never sees or handles a
password — the browser talks to the identity provider directly, and
the CLI receives back a short-lived authorization code that it
exchanges (with a locally generated PKCE verifier) for your access and
refresh tokens.

**Headless environments fall back automatically.** If Contextual
detects it's running somewhere a browser can't be opened — over SSH,
or on Linux with no display server — it falls back to the OAuth 2.0
Device Authorization Grant instead: you're given a short code and a
URL to open on any other device, and the CLI polls until that
approval completes. Same underlying account, same no-password
guarantee, just without requiring a local browser.

Your access and refresh tokens are what get stored on your machine
after login — not a password, and not anything from which a password
could be recovered. `contextual logout` revokes the refresh token
server-side (not just deleting it locally), so a copy of it left
behind on a machine you've logged out of can't be replayed to keep a
session alive.

## Activation: one network call, then offline

Licensing is node-locked: your license is validated once, at
activation, against Contextual's licensing backend, and a signed
license file is written to your machine. Every check after that is a
local, offline read of that signed file — not a network call — with a
background revalidation on a multi-hour cadence to catch things like
revocation. See `account/explanation/how-licensing-works-here` for the
full mechanics, including grace periods and moving a license between
devices.

## What the license check actually sends

Both activation and the periodic background revalidation send only
account/license identity: your machine's hardware-derived identifier
and either your license key or your access token, depending on which
step it is. Neither call includes your source code, file paths, query
text, or anything else about the codebases you've indexed — a license
check has no way to know what you've indexed, because it never asks.

## The trust boundary in one sentence

The only two things that leave your machine on their own are logging
in (an identity handshake, no password) and license validation (an
identity/entitlement check, no code). Everything else Contextual does
— indexing, search, the dependency graph — runs entirely locally; see
`trust-and-privacy/reference/data-privacy` for that full picture.
