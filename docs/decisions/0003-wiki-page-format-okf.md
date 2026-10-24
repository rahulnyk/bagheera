---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Wiki pages use OKF v0.2 with the KnowledgeX profile, validated by `kx check`

## Context and Problem Statement

The wiki schema (`council-rock/CLAUDE.md`) must define page types, front-matter, naming, links and lint rules. The architecture suggests KnowledgeX / Open Knowledge Format (OKF) "if you want portability". Should we adopt it or define our own front-matter?

## Decision Drivers

* Lint needs machine-checkable fields: who wrote a page, who checked it, when it goes stale, what it replaces or contradicts
* Reuse an existing validator instead of writing one
* Pages must stay plain markdown that GitHub renders and Claude reads
* Build as little as possible

## Considered Options

* A. OKF v0.2 + KnowledgeX profile; run `kx check` / `kx review` against the wiki
* B. Custom front-matter validated with a JSON Schema (`remark-lint-frontmatter-schema`) plus a link checker (`lychee`)
* C. No front-matter; conventions only

## Decision Outcome

Chosen option: "A", because KnowledgeX already validates required fields, broken links, kebab-case names, `stale_after`, `generated`/`verified` provenance and `supersedes`/`contradicts` relations, which covers most of wiki-lint's deterministic checks with zero new code.

Page types map onto the existing KnowledgeX vocabulary; the folder says which kind of page it is:

| Folder | Page | OKF `type` | Stale after |
| --- | --- | --- | --- |
| `services/` | Service page | `Entity` | 12 months, or sooner when the repo changes (lint) |
| `features/` | Shipped feature | `Entity` (tag `feature`) | never edited after ship, except links |
| `decisions/` | ADR (MADR sections in the body) | `Decision` | never; superseded, not edited |
| `org/` | Standards, dos and don'ts | `Principle` | never; reviewed yearly |
| `org/deploy/` | Per-environment deploy config | `Playbook` | 6 months |
| `raw/` | Immutable input (PR, spec, postmortem) | `Source` | never |

MADR status maps onto OKF status: `proposed`→`draft`, `accepted`→`stable`, `deprecated`/`superseded`→`deprecated` plus a `supersedes` link on the newer ADR.

### Consequences

* Good, because `kx check --bundle council-rock` becomes the schema check in CI on day one.
* Good, because the same pages work with any OKF tool, and with the KnowledgeX MCP server later if we want trust-aware search.
* Bad, because KnowledgeX treats every non-reserved `.md` file as a note, so root `CLAUDE.md`, `CHANGELOG.md` and `README.md` produce "missing front-matter" errors. Lint wraps `kx check` and drops findings for those three files (a few lines of shell), or we upstream an ignore list to KnowledgeX.
* Bad, because the architecture's `CHANGELOG.md` differs from OKF's reserved `log.md`. We keep `CHANGELOG.md` as the architecture says and have the curator never call `kx` commands that append to `log.md`.
* Bad, because MADR's own front-matter keys (`decision-makers`, `consulted`, `informed`) aren't OKF keys; they move into a `## Decision makers` body section to avoid unknown-key warnings.

### Confirmation

Phase 1 test T1.2: the template and example pages pass the wrapped `kx check` with zero errors; a page with a missing `description` or a broken link fails it.

## Pros and Cons of the Options

### B. Custom schema + lychee

* Good, because full control of field names.
* Bad, because we'd write and maintain the schema, staleness and relation checks ourselves.

### C. Conventions only

* Good, because nothing to learn.
* Bad, because lint can't tell stale or contradicting pages apart without fields to read.

## More Information

OKF spec: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md. KnowledgeX format: [`guides/how-to-write.md`](https://github.com/rahulnyk/KnowledgeX/blob/main/guides/how-to-write.md). Exact behaviour of `kx check` on a nested bundle with root non-note files gets verified at the start of Task 2.
