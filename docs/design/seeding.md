# Seeding

_Status: in progress · Design doc 2 of 6 · [All design docs](README.md)_

Existing services get a first-draft page from a one-off automated pass over their code, so the library isn't empty on day one (PRD K8). This doc says what runs that pass, what it reads and writes, how the drafts are reviewed, and how to run it for each repo.

Covers PRD K8. It builds on [design doc 1](knowledge-library.md): provenance (section 1), merge rules (section 2), page format (section 4) and checks (section 7).

## Principles

* **The same rules as the rest of the library.** Seeded pages follow design doc 1's principles: every statement says where it came from, and pages are brief but never without context.
* **Once per service.** Seeding writes the first page. After that, the knowledge agent's hourly run keeps it current from merged PRs (design doc 1, section 6). Seeding is never a second source of truth.

## 1. What runs the seeding pass

_Decided: [ADR 0024](../decisions/0024-seed-service-pages-with-a-claude-code-skill.md), which replaces [0009](../decisions/0009-seed-service-pages-with-deepwiki-open.md)._

A skill in the plugin, `seed-service-page`, run in Claude Code inside the service's code repo. It is the knowledge agent's (**Baloo**'s) first pass over a service: the same page format, writing rules and checks. Only the source differs: the code as it stands, rather than merged PRs.

| | |
| --- | --- |
| **Who runs it** | The owner or a platform engineer, once per service |
| **Where** | On their laptop, in a clone of the service's repo |
| **Sign-in** | Their Claude subscription (the run counts against its usage limits) or an Anthropic API key. Nothing else to install. |
| **What it reads** | The repo's code at its current commit, by searching and opening files. Where it looks is section 2. |
| **What it writes** | One draft service page in the library's format: typed header, the five service sections, at most 400 words (design doc 1, section 4) |
| **Trust** | Every statement is inferred, and its footnote names the file and commit it was read from |
| **What happens next** | A library PR for the service's owning team (section 3) |
| **Depends on** | Anthropic for the model; GitHub for the repo and the library PR |

For example:

```markdown
payments-api retries a failed charge up to 3 times over 24 hours.[^s1]

[^s1]: inferred · read from `internal/charge/retry.go` at seeonee/payments-api@a1b2c3d on 2026-11-02 · <link to the file at that commit> · reviewed in library PR #12
```

### Why not DeepWiki-Open

The PRD names DeepWiki-Open for this, and [0009](../decisions/0009-seed-service-pages-with-deepwiki-open.md) first chose it. Checking what it takes to run changed that:

| | DeepWiki-Open | Claude Code with a seeding skill |
| --- | --- | --- |
| Setup on the laptop | Docker with a database, a LiteLLM proxy, DeepWiki and Ollama | Nothing new |
| Sign-in | An Anthropic API key or Bedrock; a Claude subscription can't be used | A Claude subscription or an API key |
| Output | Its own wiki format, which would need converting into ours | Our format, with a source on every statement |
| Coverage | Indexes every file | Reads what it judges relevant; section 2 gives it a fixed list of places to look, and the owners' review catches gaps |
| Upkeep | A third-party tool whose README now points to a hosted "2.0" successor | One skill in our plugin |

**Fallback.** If the drafts for the first real repo are poor, we switch to DeepWiki-Open through its bundled LiteLLM proxy, with an Anthropic API key and Ollama's `nomic-embed-text` for indexing, pinned to a known version.

### Why not a code map

A parser can turn code into an exact map of files, functions and calls (an abstract syntax tree). That describes the code's structure, not what a service is for, how it's used, or what to watch out for, so on its own it doesn't meet K8. It may later help check that a page's `depends_on` links still match the code, if they drift. That would be one more tool to run, so it waits until there's a need.

### Note on the PRD

K8 asks for "a one-off automated pass over their code", which this meets. PRD §8, §9 (Foundations) and Appendix B name DeepWiki-Open; the wording change is listed for the owner in [the design index](README.md#prd-wording-to-update).

## Still to come

2. What the skill reads and writes: where it looks, how it treats text in the repo, large repos
3. How drafts are reviewed and verified, without swamping owners with many PRs at once
4. A per-repo runbook: order, who runs it, usage limits
