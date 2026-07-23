---
title: Create an account and link your first device
domain: web-dashboard
category: tutorial
tldr: Sign up on the website, land on /account, run contextual login in your terminal, confirm the device at /device, and you have a working CLI session.
order: 1
---

<Callout variant="tldr">
Signing up on the website and connecting your terminal are two separate
steps that meet in the middle at `/device`. This is the one full,
genuine end-to-end walk across the whole site.
</Callout>

## 1. Sign up

Go to `/sign-up` and create an account (email/password, or Google/GitHub
OAuth). You'll land on `/account` once signed in — a 14-day trial starts
automatically, no card required at this step.

## 2. Run `contextual login` in your terminal

```
contextual login
```

This opens your browser to authenticate the CLI itself — a separate
step from your website session, using OAuth with a device-code fallback
for headless machines.

## 3. Confirm at `/device`

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

## 4. You're connected

Your terminal session is now tied to your account. Run `contextual
account` any time to check your status without going back to the
website.

## See also

- `web-dashboard/how-to/link-or-relink-a-device-via-the-browser`.
- `cli/reference/login`, `cli/reference/account`.
- `web-dashboard/explanation/how-website-login-relates-to-cli-login`.
