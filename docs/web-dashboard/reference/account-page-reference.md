---
title: Account page reference
domain: web-dashboard
category: reference
tldr: "/account is a Clerk UserProfile with a custom Plan tab (status, devices, billing handoff) alongside Clerk's own Account and Security tabs, plus a Sign out tab."
order: 1
---

<Callout variant="tldr">
`/account` has four tabs: Plan (custom, Contextual-specific), Account
and Security (Clerk's own), and Sign out. This page documents exactly
what's there today — nothing aspirational.
</Callout>

## Plan tab

- Current tier and, during a trial, a countdown to the first charge.
- Device list: each entry shows activation date, IP, and a friendly
  name, with a Remove button.
- A "Manage billing" button (once you have an active subscription,
  hands off to Dodo's hosted Customer Portal) or a "Subscribe" link to
  `/payments` (if you don't).
- A Danger Zone with account deletion — see
  `web-dashboard/reference/account-deletion-reference`.

## Account / Security tabs

Standard Clerk-provided tabs — profile details, connected accounts,
password/session management. Not Contextual-specific.

## Sign out

Its own tab, since Clerk's `<UserProfile>` component (used here instead
of the popup `<UserButton>`, to support the custom Plan tab) has no
built-in sign-out menu item the way `<UserButton>` does.

## What's not here

No usage metering, no team/seat management, no API key management — none
of these exist on this page today, whether or not they're sold on
`/pricing`.

## See also

- `web-dashboard/how-to/manage-your-plan-and-devices`.
- `web-dashboard/reference/account-deletion-reference`.
