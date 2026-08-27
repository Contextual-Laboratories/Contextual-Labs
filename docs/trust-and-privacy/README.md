# Trust & privacy

What stays local, how licensing/auth works, and security disclosure scope.

- `trust-and-privacy/explanation/what-stays-local` — the no-egress-by-default
  claim: why Contextual is built local-first rather than local-optional, and
  what that means for symlinks, `.gitignore`, and the daemon's trust boundary.
- `trust-and-privacy/explanation/how-the-license-and-auth-model-works` —
  OAuth 2.0 login (browser + device-code fallback, no stored passwords) and
  what the license check actually sends over the network.
- `trust-and-privacy/reference/data-privacy` — the full inventory of what
  never leaves your machine and the two things that do (login, license
  validation).
- `trust-and-privacy/reference/security-disclosure-and-scope` — what's in
  scope for a security report, known accepted-risk limitations, and how to
  report a finding.
