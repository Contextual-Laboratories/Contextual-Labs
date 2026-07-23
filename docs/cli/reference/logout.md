---
title: "logout"
domain: cli
category: reference
tldr: "contextual logout clears local credentials and license cache from this device — it does not deactivate the device slot on your account."
order: 5
---

<Callout variant="tldr">
`contextual logout` signs you out and clears the local license cache on
this machine. It does not free up a device slot on your license — use
`contextual deactivate` for that.
</Callout>

## Usage

```
contextual logout
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual logout"},
  {output: "Logged out.\nLicense cache cleared. Run  contextual login  to re-authenticate.", muted: true}
]} />

<Callout variant="note">
`logout` and `deactivate` are not the same operation. `logout` just
clears local state on this device — your license still considers this
device active. If you're moving to a new machine and need the device
slot freed, use `contextual deactivate` before logging out, not instead
of it.
</Callout>

## See also

- `cli/reference/deactivate` — for actually freeing a device slot.
- `cli/reference/login`.
