---
title: Plans & billing reference
domain: website
category: reference
tldr: "Solo is the only live tier: $10/mo or $100/yr, 14-day trial with no card or payment info required to start, 1 active device with up to 3 swaps/month. Teams and Enterprise are waitlist-only."
order: 2
---

<Callout variant="tldr">
Solo is $10/month or $100/year. The trial is genuinely free to start —
no card, no payment info, just an explicit "Start free trial" click —
and nothing is ever charged automatically. Teams and Enterprise aren't
purchasable yet — they're a real waitlist, not a placeholder.
</Callout>

## Solo (the only live tier)

- **Price**: $10/month, or $100/year (roughly $8.33/month-equivalent).
- **Trial**: 14 days, starting only when you explicitly click "Start
  free trial" on your account page — never automatically at signup or
  email verification. No card or payment info is collected for this.
  If you never convert, the trial simply lapses at no charge, since
  there's no card on file to charge.
- **Converting to paid**: a separate, explicit action — go to the
  payments page, enter a card, and complete checkout. The charge fires
  immediately on checkout completion, not on a delay. If you still have
  trial days left when you convert, they stack on top of the new
  billing cycle at no extra charge, rather than being lost or
  scheduling a delayed first charge.
- **Refund windows**: 24 hours from any charge for monthly billing, 7
  days for annual — per charge, not per purchase. After the window
  closes, you can still cancel any time, but you ride out the already-
  paid period with no refund and no proration.
- **Device limit**: 1 concurrently active device, with up to 3
  bind/unbind swaps per calendar month (the swap count resets for
  everyone on the 1st, not on a personal rolling window). This is
  framed as an anti-fraud rate-limit, not a penalty.

## Teams and Enterprise (waitlist only)

Both tiers show real pricing tiles on `/pricing`, but the only live
action is joining a waitlist (email + tier) — there is no live
purchase path for either yet.

## What isn't enforced yet

There is no in-app plan-downgrade flow. Cancel and resume are real
inline actions on your account page, not a handoff to Dodo's hosted
portal — only "update payment method" still redirects there, since
Dodo has no API to attach a new card outside a checkout/portal session.

## See also

- `website/how-to/start-your-trial-and-subscribe-when-ready`.
- `website/explanation/what-solo-actually-includes`.
