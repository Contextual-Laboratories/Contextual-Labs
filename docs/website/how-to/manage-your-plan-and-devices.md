---
title: Manage your plan and devices from /account
domain: website
category: how-to
tldr: The Plan tab on /account shows your trial/plan status, lists every device tied to your license with a Remove button, and handles cancel/resume and invoices inline — only updating your payment method redirects to Dodo.
order: 2
---

<Callout variant="tldr">
`/account`'s Plan tab is where you check your trial/plan status, remove
a device, and manage billing — all in one place, alongside Clerk's own
Account/Security tabs. Cancel, resume, and invoice history are real
inline actions here now, not a handoff to Dodo's hosted portal.
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
`contextual logout` from the terminal — either one frees the slot.
</Callout>

## Managing billing

Once you have an active subscription, cancel and resume are real
inline actions on the Plan tab, each with their own confirmation copy —
not a handoff to Dodo's hosted portal. Invoice history is listed inline
too. The one exception is updating your payment method: that still
redirects to Dodo's checkout/portal flow, since Dodo has no API to
attach a new card outside of one.

## See also

- `website/reference/account-page-reference`.
- `cli/reference/general/logout`.
