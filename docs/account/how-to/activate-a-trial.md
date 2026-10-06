---
title: Activate a trial
domain: account
category: how-to
tldr: Sign up and explicitly click "Start free trial" on the website (no card required), then run contextual login on the device you want to use — no separate CLI trial-activation command exists.
order: 1
---

<Callout variant="tldr">
There's no dedicated "start trial" CLI command. Signing up on the
website only creates your account — you have to explicitly click
"Start free trial" on your account page to actually start the 14-day
clock (no card or payment info required). Once that's done,
`contextual login` is what activates it on a given device.
</Callout>

```
contextual login
```

<Terminal lines={[
  {command: "contextual login"},
  {output: "Opening browser for authentication...\nLogged in as you@example.com (Solo, trial ends March 4, 2026).", muted: true}
]} />

If you're on a headless or SSH-only machine where a browser can't open
locally:

```
contextual login --device-code
```

## See also

- `website/how-to/start-your-trial-and-subscribe-when-ready` —
  the website side of this same flow.
- `cli/reference/general/login`.
