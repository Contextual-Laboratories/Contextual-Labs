---
title: What Solo actually includes today vs. what's planned
domain: web-dashboard
category: explanation
tldr: Solo unlocks a real license key checked by the CLI/engine — a single active device with rate-limited swaps, framed as anti-fraud protection, not a usage penalty.
order: 2
---

<Callout variant="tldr">
What you're actually buying with Solo is a real, enforced license: one
active device at a time, checked by the CLI and engine. The device-swap
limit exists to prevent license sharing, not to restrict normal,
honest use.
</Callout>

## What's real and enforced today

- A Solo license key, tied to your account, checked by the CLI on
  login and periodically thereafter.
- One concurrently active device, with up to 3 swaps allowed per
  calendar month before the current device gets locked in place until
  the next reset.
- The 14-day trial → paid conversion mechanics described in
  `web-dashboard/reference/plans-and-billing-reference`.

## Why the device limit exists

The device-swap limit is deliberately framed as an anti-fraud
rate-limit, not a penalty for normal use — it exists to make casual
license-sharing across many machines impractical, while still allowing
a real user to move between a laptop and a desktop, or replace a
machine, without friction. The monthly reset (same day for everyone,
not a personal rolling window) is a simplicity trade-off: it means
someone could technically get a few extra swaps right around a month
boundary, and that's accepted as a known edge case rather than treated
as a bug.

## What's planned but not sold as a current feature

Teams and Enterprise tiers are visible on `/pricing` as waitlist entries
— joining the waitlist does not grant early access or any current
functionality beyond being contacted when those tiers actually launch.

## See also

- `web-dashboard/reference/plans-and-billing-reference`.
- `engine/reference/data-privacy` for what your license check does and
  doesn't transmit.
