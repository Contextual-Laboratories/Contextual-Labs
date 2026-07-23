# Account & Licensing

CLI-side licensing and device activation: trial activation, node-locked
device binding, and what to do when activation fails. The website-side
account/billing surface (the `/account` dashboard, plans, billing,
device management from the browser) is documented separately in
`web-dashboard/`, not here.

Authored here, synced to `Contextual-Labs` on merge. **First pass
authored 2026-07-23**, scoped to what's deterministic and stable today
(activation mechanics, license states, the offline-validation model).
Deliberately excludes exact billing/trial-length specifics that still
depend on Dodo going live — those get a final pass once payments are
fully real, per Boss's explicit call. See `Teams/Operations/docs-
content-buildout-joint-mission.md` for the tracked status.
