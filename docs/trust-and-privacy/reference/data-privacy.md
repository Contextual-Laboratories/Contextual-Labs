---
title: "Data Privacy: What Stays Local"
domain: trust-and-privacy
category: reference
tldr: Indexing and querying your code never leave your machine — no remote embedding calls, no external telemetry export by default, no source code transmitted anywhere. The only outbound network traffic Contextual makes on its own is account login and license validation.
order: 7
related:
  - models/explanation/embedding-model-stack.md
  - getting-started/explanation/architecture-overview.md
  - indexing/reference/contextualignore-reference.md
---

<Callout variant="tldr">
Your source code is never sent anywhere. Chunking, embedding, graph
extraction, blame analysis, and every query all run locally, against a
local database, using a local model. The only network calls Contextual
makes on its own are for account login and license validation — neither
of which transmits your code.
</Callout>

## What never leaves your machine

**Your source code.** Chunking, embedding, dependency-graph extraction,
and blame analysis all run as local processes against files already on
your disk. Nothing about this pipeline sends file contents, chunk text,
or embeddings to a remote service. See
`indexing/explanation/how-indexing-works` for the full pipeline.

**Your queries.** `search`, `nexus_search`, and every other MCP tool call
run entirely against your local LanceDB store. A query embedding is
computed locally, by the same local model used at index time — it never
goes to a hosted embedding API.

**The embedding model itself.** A single local, quantized ONNX model
handles all embedding, running on your CPU via ONNX Runtime. Model
weights are downloaded once (`contextual fetch`); every embedding call
after that runs against the local copy with zero network involvement.
See `models/explanation/embedding-model-stack`.

**Telemetry, by default.** Contextual's tracing instrumentation is real
OpenTelemetry, not a stub — but its export target is your own local
LanceDB tables (`otel_spans`, `otel_logs`), not an external collector.
This local export is on by default (`export_to_lancedb`), since
`get_telemetry`, `diagnose_issue`, and `contextual activity` all depend
on it to answer questions about your own daemon's behavior — see
`observability/explanation/how-tracing-and-logging-work`. There is no
telemetry egress to Contextual Labs or anyone else, regardless of this
setting.

## What does make a network call, and why

**Account login.** `contextual auth login` uses a standard OAuth 2.0
flow (Authorization Code + PKCE via your browser, with an automatic
device-code fallback for headless environments) against Contextual's own
backend — this is how your account and license get associated with your
machine. No password is ever collected or transmitted; there's no legacy
password flow to compare it against.

**License validation.** A signed license file is checked locally
(offline) against a cached copy on your machine for the common case; the
license is validated against Contextual's licensing backend during
activation and periodically thereafter to check for issues like
revocation, not per-query and not per-tool-call.

Neither of these transmits your source code, your search queries, file
paths, or any derived data about your codebase — they carry only
account/license identity information.

## What stays out of the index in the first place

Beyond "your code stays local once indexed," Contextual is also careful
about what it reads in the first place:

- **Symlinks are never followed** during file discovery — a symlink
  pointing outside your project root (`~/.ssh/id_rsa`, `/etc/`) is
  skipped outright, not read and indexed. See
  `indexing/reference/contextualignore-reference`.
- **`.gitignore` is honored automatically**, not just
  `.contextualignore` — a gitignored `.env` file or other secret never
  ends up in the searchable index just because it wasn't also added to
  `.contextualignore`.

## Local-machine trust boundary

The MCP daemon only accepts connections from your own machine (loopback
only), and every request requires a bearer token — on macOS/Linux this
is backed by a Unix domain socket with owner-only filesystem permissions;
on Windows, by an ACL-hardened token file plus a same-process
verification check on the daemon's TCP port (see
`indexing/reference/platform-support` for the platform-specific detail).
This is a local-machine trust boundary, not a network-facing one — the
daemon was never designed to be reachable from another machine on your
network.

## The bottom line

If you're evaluating Contextual for a codebase you can't send to a third
party: indexing, retrieval, and the graph all run as local computation
against a local database, using a local model, with no code, query, or
derived-data egress. The only thing that talks to the outside world is
account and license management — and that boundary is enforced by what
the code actually does, not by a policy promise layered on top.
