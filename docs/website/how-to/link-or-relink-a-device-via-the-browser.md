---
title: Link or re-link a device via the browser
domain: website
category: how-to
tldr: /device confirms a CLI login request in your browser — check the shown IP and timing match what you just did, complete the Turnstile challenge, and confirm.
order: 3
---

<Callout variant="tldr">
`/device` is where you approve a CLI login request from your browser.
It shows exactly which request you'd be confirming — check that against
what you actually just did.
</Callout>

## What you'll see

A banner reading something like "Confirming a CLI sign-in requested
from `<ip>`, `<time>` ago," followed by a Cloudflare Turnstile
challenge, then a confirm button.

<Callout variant="warning">
If the IP address or timing shown doesn't match something you just
did, don't confirm it — that mismatch is exactly what this page is
designed to surface. A request you didn't make appearing here is a
sign someone else triggered a device-login flow for your account.
</Callout>

## Re-linking after a device swap

If you've removed a device from `/account` and want to link a new one,
the flow is identical: run `contextual login` on the new machine, then
confirm at `/device` when it appears.

## See also

- `website/tutorials/create-an-account-and-link-your-first-device`.
- `cli/reference/general/login`, `cli/how-to/move-your-license-to-a-new-machine`.
