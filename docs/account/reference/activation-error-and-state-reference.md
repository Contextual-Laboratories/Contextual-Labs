---
title: Activation error & state reference
domain: account
category: reference
tldr: "Every license state Contextual reports maps to one specific, named reason — not_authenticated, tampered, wrong_device, clock_tampered, or a grace-period variant — never a generic failure."
order: 1
---

<Callout variant="tldr">
License checks never fail generically. Every non-active state has a
specific reason, surfaced through `contextual account`. This page lists
what each one means and what to do about it.
</Callout>

## States you'll see day to day

- **Active** — a normal, current license, outside any trial window.
- **Trial · N day(s) remaining** — inside your trial period.
- **Grace period — N day(s) remaining** — your trial or billing period
  ended and you're in the grace window before enforcement. Contextual
  keeps working during this window; it's a soft warning, not a lock.
- **Inactive (reason)** — something is preventing normal validation; the
  specific reason is always included, never a bare "inactive."

## Specific inactive reasons

- **`not_authenticated`** — no license file exists on this device yet.
  Run `contextual login`.
- **`tampered`** — the local license file failed its integrity check.
  This can happen if the file was manually edited or corrupted. Run
  `contextual logout` then `contextual login` again to get a fresh copy.
- **`wrong_device`** — this license file is bound to a different
  machine than the one you're running it on. If you're intentionally
  moving machines, see `cli/how-to/move-your-license-to-a-new-machine`
  — you need to deactivate on the old device first.
- **`clock_tampered`** — your system clock doesn't match what's
  expected. Check that your device's date and time are set correctly
  (including timezone) and try again.
- **`grace:blocked`** / **`grace_expired:blocked`** — your subscription
  was cancelled or a payment failed; you're either still in the grace
  window (`grace:blocked`, still working) or past it
  (`grace_expired:blocked`, enforcement applies).
- **`grace:expired`** / **`grace_expired:expired`** — your license
  period (trial or paid) ended; same in-grace vs. past-grace
  distinction as above.

## Why this matters

License validation happens entirely offline, reading only a local,
cryptographically signed file — see
`account/explanation/how-licensing-works-here` for the full mechanism.
That's exactly why a specific reason is always available: the check
itself is deterministic and local, not a network call that could fail
in an unexplained way.

## See also

- `account/how-to/troubleshoot-a-failed-activation`.
- `cli/reference/general/account`, `cli/reference/general/doctor`.
