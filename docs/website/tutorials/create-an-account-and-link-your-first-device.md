---
title: Create an account and link your first device
domain: website
category: tutorial
tldr: Sign up on the website, start your trial explicitly on /account (no card required), run contextual login in your terminal, confirm the device at /device, and you have a working CLI session.
order: 1
---

<Callout variant="tldr">
Signing up, starting a trial, and connecting your terminal are three
separate, explicit steps — signing up alone doesn't start a trial, and
`contextual login` won't work until you have an active license. This is
the one full, genuine end-to-end walk across the whole site.
</Callout>

## 1. Sign up

Go to `/sign-up` and create an account (email/password, or Google/GitHub
OAuth). You'll land on `/account` once signed in — this creates your
account only, no trial yet.

## 2. Start your trial

Click "Start free trial" on `/account`. This is what actually starts
the 14-day clock — no card or payment info is collected for it. Until
you do this, the account has no active license, and `contextual login`
below will fail with a "you don't have an active subscription yet"
error rather than succeeding.

## 3. Run `contextual login` in your terminal

```
contextual login
```

This opens your browser to authenticate the CLI itself — a separate
step from your website session, using OAuth with a device-code fallback
for headless machines.

## 4. Confirm at `/device`

You'll land on a page showing "Confirming a CLI sign-in requested from
`<ip>`, `<time>` ago" — a Cloudflare Turnstile challenge appears here
too, a real bot-protection step, not a glitch. Check that the IP and
timing actually match what you just did before confirming; this banner
exists specifically so a phishing attempt aimed at your account has a
visible mismatch for you to notice.

<Terminal lines={[
  {command: "contextual login"},
  {output: "Opening browser for authentication...\nLogged in as you@example.com (Solo, trial ends March 4, 2026).", muted: true}
]} />

## 5. You're connected

Your terminal session is now tied to your account. Run `contextual
account` any time to check your status without going back to the
website.

## See also

- `website/how-to/link-or-relink-a-device-via-the-browser`.
- `cli/reference/general/login`, `cli/reference/general/account`.
- `website/explanation/how-website-login-relates-to-cli-login`.
