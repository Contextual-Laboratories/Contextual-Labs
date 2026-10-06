---
title: Start your trial and subscribe when ready
domain: website
category: how-to
tldr: Sign up, then explicitly click "Start free trial" on your account page for 14 days — no card or payment info required to start, and nothing is ever charged automatically.
order: 1
---

<Callout variant="tldr">
Signing up creates your account only — it does not start a trial.
Click "Start free trial" on your account page when you're ready, and
you get 14 days with no card or payment info collected at all. If you
do nothing else, the trial simply lapses; there's no card on file to
charge.
</Callout>

## Starting the trial

Sign up, then go to your account page and click "Start free trial."
That's the only thing that starts the clock — it never starts
automatically at signup or email verification. No card or payment
info is requested for this step.

## During the trial

Use the CLI normally — `contextual login` ties your terminal to the
trial account, and every feature works exactly as it would on a paid
plan.

## Subscribing before or after the trial ends

Go to `/payments` while signed in. You'll see a consent step (two
checkboxes covering the terms you're agreeing to) before being
redirected to Dodo's hosted checkout page. The charge fires immediately
on checkout completion — it's never deferred. If you still have trial
days left when you subscribe, they stack on top of the new billing
cycle at no extra charge, rather than being lost.

<Callout variant="warning">
There is currently no in-app way to downgrade a subscription. Cancel
and resume, however, are real inline actions on your account page once
you have an active subscription — not a handoff to Dodo's hosted
portal. Only "update payment method" still redirects to Dodo, since
there's no API to attach a new card outside a checkout/portal session.
</Callout>

## What happens if you do nothing

If you never start a trial, nothing happens — no account is put on any
kind of clock. If you start a trial and let it run out without
subscribing, it simply lapses at no charge, since no card was ever
collected. Refund windows apply once you do subscribe, per charge
afterward and not per purchase — see
`website/reference/plans-and-billing-reference` for the exact
numbers.

## See also

- `website/how-to/manage-your-plan-and-devices`.
- `website/reference/plans-and-billing-reference`.
