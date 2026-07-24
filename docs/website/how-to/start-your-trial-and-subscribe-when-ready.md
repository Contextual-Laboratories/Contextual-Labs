---
title: Start your trial and subscribe when ready
domain: website
category: how-to
tldr: Sign up for a 14-day trial with no charge — a card is required at signup but you're not billed until day 15 unless you subscribe from /payments first.
order: 1
---

<Callout variant="tldr">
Signing up starts a 14-day trial. A card is required at signup, but
you are not charged during the trial — the first charge only fires
automatically on day 15 if you haven't cancelled.
</Callout>

## During the trial

Use the CLI normally — `contextual login` ties your terminal to the
trial account, and every feature works exactly as it would on a paid
plan.

## Subscribing before the trial ends

Go to `/payments` while signed in. You'll see a consent step (two
checkboxes covering the terms you're agreeing to) before being
redirected to Dodo's hosted checkout page.

<Callout variant="warning">
There is currently no in-app way to downgrade a subscription — only
cancel, via Dodo's hosted billing portal (accessible from `/account`
once you have an active subscription). If you need to change plans
rather than cancel outright, that's a real, current limitation, not
something you're missing in the UI.
</Callout>

## What happens if you do nothing

Your card is charged automatically on day 15 unless you cancel first.
Refund windows apply per charge afterward, not per purchase — see
`website/reference/plans-and-billing-reference` for the exact
numbers.

## See also

- `website/how-to/manage-your-plan-and-devices`.
- `website/reference/plans-and-billing-reference`.
