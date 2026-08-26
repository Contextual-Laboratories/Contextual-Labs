---
title: How node-locked licensing works here
domain: account
category: explanation
tldr: Your license is validated automatically and periodically in the background, entirely offline, using a cryptographically signed license file — your code is never read or transmitted for this or any other purpose.
order: 1
---

<Callout variant="tldr">
Licensing here is node-locked: one license, one active device at a
time, checked locally against a signed file on your machine — not by
calling home on every use.
</Callout>

## Activation is a one-time network step

The only point where checking your license requires an internet
connection is activation itself — the moment you run `contextual login`
on a device for the first time. That call registers this specific
machine against your license and writes a signed license file locally.

## Everything after that is offline

Every check after activation reads only that local, cryptographically
signed file. Your license is validated automatically and periodically
in the background — validation happens entirely offline, and your code
is never read or transmitted for this or any other purpose. This is
also why every inactive-license reason (see
`account/reference/activation-error-and-state-reference`) is specific
and immediate rather than a vague network-dependent failure: the check
itself doesn't depend on the network at all.

## Grace periods exist for real-world interruptions

If your license period ends, or the background refresh can't reach the
license server, a grace period keeps Contextual working normally for a
short window before any restriction applies — not a hard cutoff at the
exact moment something changes.

## One device at a time, by design

A Solo license is bound to a single active device. This is enforced at
activation, not layered on top afterward — trying to activate a second
device without deactivating the first is exactly the `wrong_device`
state covered in the reference page. Moving machines is a normal,
supported action (`contextual logout` then `contextual login` on
the new one), not something you have to work around.

## See also

- `account/reference/activation-error-and-state-reference`.
- `trust-and-privacy/reference/data-privacy` for the broader "what stays local"
  picture beyond just licensing.
