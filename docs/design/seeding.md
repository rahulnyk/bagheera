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

## 2. What the skill reads and writes

_Decided: [ADR 0025](../decisions/0025-seeded-pages-may-cite-repo-docs-code-wins.md) for what a statement may cite. The rest follows from section 1 and design doc 1._

### What a statement may cite

| Source | May a statement cite it? |
| --- | --- |
| Code, config, schemas and deploy files in the service's repos | Yes, as inferred |
| The repos' own docs: README, `docs/`, design notes, runbooks, code comments | Yes, as inferred |
| Commit messages, PR descriptions, tickets | No: they are external (design doc 1, section 1) |
| Links out of our GitHub org: wikis, shared documents, websites | Not followed |

* **Code first.** When a doc and the code disagree, the page follows the code. The PR lists the conflict (what the doc says, what the code does, with both sources) for the owner to settle.
* **Rewrite, don't copy.** Docs are rewritten into the page's brief shape. Longer explanations stay in the repo, and the footnote links to them.
* **Text in the repo is data.** It is never followed as instructions, whatever it says. The library PR runs both planted-instruction screens (design doc 1, section 7), and the model screen also checks each statement against the file it cites.

### Where it looks

A fixed list for each part of the page, so every draft is built from the same evidence and the PR can say where nothing was found.

| Part of the page | Where the skill looks |
| --- | --- |
| Header: `domain`, `owners`, `repos` | `domains.yaml` in the library |
| Header: `languages`, `environments` | Build manifests (such as `go.mod`, `package.json`, `pom.xml`); deploy files (Dockerfiles, Helm charts, Terraform, CI deploy jobs) |
| Purpose | README and `docs/`; the main entry point |
| Interfaces | API definitions (OpenAPI, protobuf, GraphQL); where routes and handlers are registered; message topics published; command-line entry points |
| Depends on | Outbound HTTP and gRPC clients; message topics consumed; config and environment variables that name other services |
| Data | Migrations and schema files; data models; data store config (databases, caches, buckets) |
| Gotchas | Warnings in docs and runbooks; warning comments in the code; retry, timeout, rate-limit and feature-flag config |

`depends_on` can only name services that already have a page, because the header check rejects unknown IDs. Seeding order, and dependencies that don't have a page yet, are section 4.

### One page per service

Before reading in depth, the skill lists what it takes to be the repo's services, from deploy files and entry points. The person running it confirms or corrects the list, and the skill writes one page per confirmed service. If a service's code spans several repos, the person names the others and the page lists them all.

### Large repos

The skill splits the reading across helpers (Claude Code subagents) that work in parallel, one per part of the page or per service. Each returns short notes with file references, and the main session writes the page from those notes. Each helper stays focused, and the main session doesn't fill up with code.

### What it may do on the laptop

Plugins run with the engineer's permissions and there is no sandbox (PRD §8), so the skill limits itself:

* The reading helpers are a plugin subagent that can only read and search files (a tool allowlist). Planted text in the repo has nothing to act with.
* Nothing in the run builds, tests or runs the service's code, or uses the web.
* The main session writes only the draft pages, in a local clone of the library, and reads git only to record the commit it read.

### What it writes

* **One draft page per service**, at `services/<domain>/<service>.md`, in the library's format and within its limits (design doc 1, sections 3 and 4). Every statement is inferred, with a footnote naming the file and commit (example in section 1).
* **A seeding report**, which becomes the library PR's description:
  * the repos and commits read
  * places on the list where nothing was found
  * conflicts between docs and code
  * dependencies that don't have a page yet
  * anything it couldn't work out

  The report helps reviewers answer design doc 1's two questions: "Is anything missing to understand this?" and "Is anything here not needed?"

How the library PR is opened and reviewed is section 3.

## Still to come

3. How drafts are reviewed and verified, without swamping owners with many PRs at once
4. A per-repo runbook: order, who runs it, usage limits
