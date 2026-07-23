---
title: First-time activation walkthrough
domain: account
category: tutorial
tldr: Sign up on the website, then run contextual login in your terminal — that one command activates your license on this device and starts your trial clock.
order: 1
---

<Callout variant="tldr">
There's no separate "activation" step beyond `contextual login`. Signing
up on the website creates your account and trial; running `contextual
login` is what actually activates the license on a given device.
</Callout>

## 1. Create your account

Sign up on the website first (see `web-dashboard/tutorials/create-an-
account-and-link-your-first-device` for that side of the flow). This is
what provisions your trial.

## 2. Activate this device

```
contextual login
```

<Terminal lines={[
  {command: "contextual login"},
  {output: "Opening browser for authentication...\nLogged in as you@example.com (Solo, trial ends March 4, 2026).", muted: true}
]} />

This is the one-time activation step — it registers this specific
machine against your license. Everything after this point (checking
whether your license is still valid) happens locally, offline, every
time you use Contextual.

## 3. Confirm it worked

```
contextual account
```

<Terminal lines={[
  {command: "contextual account"},
  {output: "you@example.com\nTier: Solo\nStatus: Trial · 12 day(s) remaining\nTrial ends: March 4, 2026\nLast verified: just now", muted: true}
]} />

## Moving to a second machine later

A Solo license activates one device at a time. See
`cli/how-to/move-your-license-to-a-new-machine` when you need to switch.

## See also

- `cli/reference/login`, `cli/reference/account`.
- `account/explanation/how-licensing-works-here`.
