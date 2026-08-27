# Account & Licensing

CLI-side licensing and device activation: trial activation, node-locked
device binding, and what to do when activation fails. The website-side
account/billing surface (the `/account` dashboard, plans, billing,
device management from the browser) is documented separately in
`website/`, not here.

This section covers what's deterministic and stable today — activation
mechanics, license states, and the offline-validation model. Exact
billing and trial-length specifics live in `website/` instead, since
those are governed by the plan/payment details shown there.
