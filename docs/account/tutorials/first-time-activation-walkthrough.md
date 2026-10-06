---
title: First-time activation walkthrough
domain: account
category: tutorial
tldr: Sign up on the website, start your trial explicitly (no card required), then run contextual login in your terminal — that one command activates your license on this device.
order: 1
---

<Callout variant="tldr">
There's no separate "activation" step beyond `contextual login`. Signing
up on the website only creates your account — you still need to
explicitly start a trial before `contextual login` has a license to
activate.
</Callout>

## 1. Create your account and start your trial

Sign up on the website first (see `website/tutorials/create-an-
account-and-link-your-first-device` for that side of the flow), then
click "Start free trial" on your account page — no card or payment
info required. Skipping this step means `contextual login` below will
fail with a "you don't have an active subscription yet" error, since
there's no license to activate yet.

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

- `cli/reference/general/login`, `cli/reference/general/account`.
- `account/explanation/how-licensing-works-here`.
