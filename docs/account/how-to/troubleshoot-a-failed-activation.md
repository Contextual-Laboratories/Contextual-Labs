---
title: Troubleshoot a failed activation
domain: account
category: how-to
tldr: Run contextual account to see the exact status reason, then check it against the activation error reference — most failures are a specific, named state, not a generic failure.
order: 2
---

<Callout variant="tldr">
A failed or unexpected license state always has a specific, named
reason — check `contextual account`'s output against
`account/reference/activation-error-and-state-reference` before
assuming something's broken.
</Callout>

## Step 1: check the actual status

```
contextual account
```

If this says "Not authenticated," you haven't successfully run
`contextual login` yet on this device — start there.

## Step 2: match the reason against the reference

If `contextual account` shows an inactive or unusual status, the reason
string it reports maps to a specific, known cause — see
`account/reference/activation-error-and-state-reference` for the full
list (wrong device, tampered license file, clock manipulation detected,
grace period active or expired).

## Step 3: run doctor

```
contextual doctor
```

License/activation problems don't show up as their own `doctor` line
directly, but a broken **Configuration** or **Directories** check can
be the underlying cause of an activation that looks like it failed for
no reason.

## If you moved machines without deactivating first

A Solo license only activates on one device. If login fails with a
device-mismatch reason and you're trying to move machines, see
`cli/how-to/move-your-license-to-a-new-machine` — you need to
deactivate on the old device first.

## See also

- `account/reference/activation-error-and-state-reference`.
- `cli/reference/general/account`, `cli/reference/general/doctor`.
