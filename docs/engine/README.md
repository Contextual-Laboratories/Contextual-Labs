# engine

Everything about how Contextual actually works under the hood: indexing,
retrieval, the knowledge graph, temporal intelligence, storage, and
operating the daemon — plus two cross-cutting sub-clusters that aren't
general Diátaxis categories:

- **`troubleshooting/`** — symptom-indexed lookups ("entity not found,"
  "daemon acting up," "workspace won't connect"). Modeled on an
  error-code reference: symptom → what it means → likely cause → fix.
- **`observability/`** — logs, telemetry, and how to read a `doctor`
  report.

This domain replaces the earlier `concepts/` placeholder — the same
architecture/graph/temporal content lives here, with real reference-grade
depth (config keys, schema, sizing, language support) that a 6-page
"explanation only" treatment couldn't honestly cover, plus the two
sub-clusters above.

Disclosure discipline throughout: mechanism is explained in depth (how
indexing/retrieval/the graph/temporal blame work, with real diagrams);
literal tuned constants (exact weights, thresholds, batch sizes, model
checkpoint names) are not.

Authored here, in `Contextual/docs/Public/`. Synced automatically to
`Contextual-Labs` on merge to main — never hand-edit the copy over there.
