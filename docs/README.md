# Documentation

This tree is a **published mirror**. Technical content is authored once in
the private `Contextual` engine repo (`docs/Public/`) and synced here
automatically via a GitHub Action on merge — don't hand-author technical
content directly in this repo, it'll be overwritten/diverge from the source
of truth. `README.md` files (this one, and the section-level ones below)
and anything under `.github/` are the exception — those are authored
directly in this repo.

## Structure

Docs are split by product surface, and each surface follows the
[Diátaxis](https://diataxis.fr/) framework — four categories organized by
what the reader is trying to do, not by feature:

```
docs/
├── cli/            the Python CLI (contextual-engine on PyPI)
├── mcp-server/      the MCP server and its tools
└── web-dashboard/   the web dashboard
    ├── tutorials/    learning-oriented — step by step, for first-time setup
    ├── how-to/       problem-oriented — "how do I do X"
    ├── reference/    information-oriented — exhaustive, dry, accurate (CLI flags, MCP tool schemas, config keys)
    └── explanation/  understanding-oriented — architecture, concepts, why things work the way they do
```

This exact shape (`<surface>/<diátaxis-category>/`) is the sync target —
`Contextual/docs/Public/` mirrors it 1:1 so the sync Action is a plain file
copy, no path remapping.

No versioning yet — this is a single, always-current "latest" tree
(pre-1.0; see `Teams/DRs/PERPLEXITY-DR-docs-socials.md` Part B for why that's
deliberate, not an oversight).
