---
title: Manage your plan and devices from /account
domain: website
category: how-to
tldr: The Plan tab on /account shows your trial/plan status, lists every device tied to your license with a Remove button, and links to Dodo's billing portal once you have an active subscription.
order: 2
---

<Callout variant="tldr">
`/account`'s Plan tab is where you check your trial/plan status, remove
a device, and reach Dodo's hosted billing portal — all in one place,
alongside Clerk's own Account/Security tabs.
</Callout>

## Checking your status

The Plan tab shows your current tier and, during a trial, a countdown.
This is read from your account's real, live state — not a cached or
placeholder value.

## Removing a device

Each device in your device list shows its activation date and a
friendly name, with a Remove button. A device stays listed until the
removal is actually confirmed by the backend — it won't silently
disappear if the request fails partway.

<Callout variant="note">
Solo licenses allow one active device at a time, with up to three
swaps per calendar month (resetting on the 1st for everyone, not on a
rolling per-user window). Removing a device here is the same action as
`contextual deactivate` from the terminal — either one frees the slot.
</Callout>

## Managing billing

Once you have an active subscription, a "Manage billing" button appears
and hands off to Dodo's hosted Customer Portal — cancellations,
payment-method updates, and invoice history all happen there, not on
this site directly.

## See also

- `website/reference/account-page-reference`.
- `cli/reference/general/deactivate`.
