---
title: "logout"
domain: cli
category: reference
tldr: "contextual logout signs out AND deactivates this device in one step — it verifies the account signed in at contextuallabs.dev in your browser, then wipes every local credential and frees the device slot."
order: 5
---

<Callout variant="tldr">
`contextual logout` signs you out and fully deactivates this device in
one step. It opens your browser to confirm the account signed in at
contextuallabs.dev matches this device's account, then revokes this
device's license and clears every local credential. There is no
separate `deactivate` command — logging out always frees the device
slot.
</Callout>

## Usage

```
contextual logout
```

A browser window opens to verify which account is currently signed in
at contextuallabs.dev. If it matches the account this device belongs
to, the device is deactivated and every local credential/license file
is removed.

<Callout variant="note">
Unlike `login`, this check never shows Clerk's sign-in UI at all — it
can't, by design: it needs "nobody is signed in" to be a valid answer,
not a login prompt. The browser hits `/oauth/whoami`, which is a pure
redirect endpoint (no HTML of its own, signed in or not) straight back
to the CLI's own local loopback listener with the result as query
parameters. Whatever appears in the tab — the "Account check complete"
confirmation, or nothing at all if it never got that far — is rendered
entirely by the CLI process on your machine, exactly like the
loopback page `login` shows.
</Callout>

<Terminal lines={[
  {command: "contextual logout"},
  {output: "Opening browser to verify your Contextual account…\n\n✓ logged out\n  account        you@example.com\n  device         deactivated  ✓\n  swaps left     2  this month\n  resets         2026-09-01", muted: true}
]} />

<Callout variant="note">
Solo licenses allow one active device at a time, with up to three
device swaps per calendar month (resetting on the 1st for everyone,
not a rolling per-user window). If you've already used all three this
month, `logout` stops before touching anything — this device stays
fully logged in until the reset.
</Callout>

### If the browser shows a different account

`logout` will refuse to deactivate this device if a different account
(or no account) is signed in at contextuallabs.dev in your browser —
sign in as the account this device belongs to there, then run
`contextual logout` again.

### `--force`

For headless machines or when you can't reach the matching account in
a browser:

```
contextual logout --force
```

This skips the browser check entirely. Because it's an irreversible,
unverified device wipe, it shows the full consequences up front and
requires you to type your account email back as confirmation before
anything happens.

## See also

- `cli/how-to/move-your-license-to-a-new-machine` — moving to a new device.
- `cli/reference/general/login`.
