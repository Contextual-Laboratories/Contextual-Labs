---
title: Move your license to a new machine
domain: cli
category: how-to
tldr: On the old machine run contextual logout to free the device slot, then contextual login on the new one — a Solo license is single-device.
order: 7
---

<Callout variant="tldr">
Solo licenses are single-device. To move machines: `contextual logout`
on the old one first, then `contextual login` on the new one.
</Callout>

**On the old machine:**

```
contextual logout
```

<Terminal lines={[
  {command: "contextual logout"},
  {output: "Opening browser to verify your Contextual account…\n\n✓ logged out\n  account        you@example.com\n  device         deactivated  ✓\n  swaps left     2  this month\n  resets         2026-09-01", muted: true}
]} />

`logout` verifies the account signed in at contextuallabs.dev in your
browser, then revokes this device's license and clears every local
credential — it always frees the device slot, in one step.

**On the new machine:**

```
contextual login
```

<Callout variant="note">
No browser handy on the old machine (headless/SSH)? Use `contextual
logout --force` — it skips the browser check but asks you to type your
account email back to confirm before wiping anything, since it's
otherwise unverified.
</Callout>

## See also

- `cli/reference/general/logout`, `cli/reference/general/login`.
