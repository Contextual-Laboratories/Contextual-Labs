---
title: Activate a trial
domain: account
category: how-to
tldr: Sign up on the website (this starts your trial automatically), then run contextual login on the device you want to use — no separate trial-activation command exists.
order: 1
---

<Callout variant="tldr">
There's no dedicated "start trial" command. Signing up on the website
starts your trial; `contextual login` is what activates it on a given
device.
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

- `web-dashboard/how-to/start-your-trial-and-subscribe-when-ready` —
  the website side of this same flow.
- `cli/reference/login`.
