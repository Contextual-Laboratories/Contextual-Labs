---
title: How website login relates to CLI login
domain: web-dashboard
category: explanation
tldr: Your website session and your CLI session are two separate authentication systems that happen to share one account — logging into one doesn't automatically log in the other.
order: 1
---

<Callout variant="tldr">
Signing in on the website and running `contextual login` in your
terminal are two genuinely different authentication flows, not one
mechanism with two front ends. They're linked to the same account, but
neither one implies the other.
</Callout>

## The website session

A normal browser session, backed by Clerk: email/password or Google/
GitHub OAuth, cookies, the whole standard web-auth picture. This is what
lets you view `/account`, manage billing, and remove devices.

## The CLI session

A separate OAuth flow, purpose-built for a terminal: your browser opens
briefly to authenticate, then a signed token comes back to your local
CLI, tied to your device. If a browser can't open locally (a
headless/SSH machine), `--device-code` gives you a code to enter from
any browser instead — including a completely different machine.

## Why they're kept separate

A website session and a CLI session have different security properties
and different lifetimes — conflating them would mean a browser cookie
expiring could silently break your terminal's access, or vice versa.
Keeping them as two systems that reference the same account avoids that
coupling entirely.

## What this means practically

Signing out on the website (`contextual` account page) does not log
your CLI out, and running `contextual logout` does not sign you out of
the website. Each has its own explicit sign-out action.

## See also

- `web-dashboard/tutorials/create-an-account-and-link-your-first-device`.
- `cli/reference/login`, `cli/reference/logout`.
