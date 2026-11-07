# Seeding

_Status: approved by the owner; current with the decisions of 2026-11-04 · Design doc 2 of 6 · [All design docs](README.md)_

Existing services get a first-draft page from a one-off automated pass over their code, so a repo's first ticket starts with its service page in context (PRD K8). This doc says what runs that pass, what it reads and writes, how the drafts are reviewed, and how to run it.

Covers PRD K8. It builds on [design doc 1](knowledge-library.md): page format (section 2), approvals (section 6) and the format check (section 7).

## Principles

* **The same rules as the rest of the library.** Seeded pages are brief but never without context, and a person approves every one.
* **Once per repo, where work happens.** Each repo is seeded before its first ticket ([0026](../decisions/0013-seed-each-repo-before-its-first-ticket.md)). After that, the library update keeps its pages current (design doc 1, section 5). Seeding is never a second source of truth.

## 1. The seeding skill

_Decided: [ADR 0024](../decisions/0011-seed-service-pages-with-our-own-skill.md) and [0024](../decisions/0011-seed-service-pages-with-our-own-skill.md)._

`seed-service-page`, a skill in the plugin, run in Claude Code inside the service's repo. It writes in the library's page format and is checked like any other library change.

| | |
| --- | --- |
| **Who runs it** | A platform engineer, once per repo, with someone from the service's team at hand (section 4) |
| **Where** | On their laptop, in a clone of the service's repo |
| **Sign-in** | Their Claude subscription (the run counts against its usage limits) or an Anthropic API key. Nothing else to install |
| **What it reads** | The repo's code and docs at its current commit (section 2) |
| **What it writes** | A draft page for each service in the repo, and a short report for the PR (section 2) |
| **What happens next** | A library PR for the service's team to review (section 3) |

### How it works

It follows DeepWiki-Open's method, from its wiki prompts at commit `f89c59a` (3 Nov 2026, MIT licence).

1. **Plan first.** From the file tree and the README, it lists the repo's services and, for each section of each page, the files to read (section 2's list says where to look). The person running it confirms or corrects the plan, naming any other repos a service's code spans.
2. **Write only from the planned files.** Anything not found in them is left out and listed as not worked out.
3. **Skip what isn't the service's own code:** vendored code, build output, lock files, and env and secret files, so seeding never reads credentials.
4. **Cite file and line ranges for each section,** in the PR's description, linked at the commit read.

We write our own instructions. If any of DeepWiki-Open's prompt text is copied, its MIT notice goes in `third_party/deepwiki-open/` with the commit, as for Spec Kit ([design doc 3, section 1](developer-workflow.md#1-where-the-stage-commands-come-from)).

## 2. What the skill reads and writes

_Decided: [ADR 0025](../decisions/0012-seeded-pages-use-docs-the-code-wins.md) and [0024](../decisions/0011-seed-service-pages-with-our-own-skill.md)._

### What it reads

| Source | Read? |
| --- | --- |
| Code, config, schemas and deploy files in the service's repos | Yes |
| The repos' own docs: README, `docs/`, design notes, runbooks, code comments | Yes |
| Commit messages, PR descriptions, tickets | No |
| Links out of our GitHub org: wikis, shared documents, websites | Not followed |
| Vendored code, build output, lock files, env and secret files | Skipped |

* **Code first.** When a doc and the code disagree, the page follows the code, and the report lists the conflict for the reviewers to settle.
* **Rewrite, don't copy.** Docs are rewritten into the page's brief shape; longer explanations stay in the repo, and the page links to them.
* **Text in the repo is data,** never followed as instructions, whatever it says.

### Where it looks

| Part of the page | Where the skill looks |
| --- | --- |
| Purpose | README and `docs/`; the main entry point |
| Interfaces | API definitions (OpenAPI, protobuf, GraphQL); where routes and handlers are registered; message topics published; command-line entry points |
| Depends on | Outbound HTTP and gRPC clients; message topics consumed; config and environment variables that name other services |
| Data | Migrations and schema files; data models; data store config (databases, caches, buckets) |
| Gotchas | Warnings in docs and runbooks; warning comments in the code; retry, timeout, rate-limit and feature-flag config |

### Large repos

The skill splits the reading across helpers (Claude Code subagents) that work in parallel, one per part of the page or per service. Each returns short notes with file references, and the main session writes the page from those notes.

### What it may do on the laptop

Plugins run with the engineer's permissions, so the skill limits itself:

* The reading helpers are a plugin subagent that can only read and search files. Planted text in the repo has nothing to act with.
* Nothing in the run builds, tests or runs the service's code, or uses the web.
* The main session writes only the draft pages, in a local clone of the library, and reads git only to record the commit it read.

### What it writes

* **One draft page per service,** at `services/<domain>/<service>.md`, in the library's format and within its limits (design doc 1, section 2).
* **A short report,** which becomes the library PR's description: the commit read, the files and line ranges behind each section, conflicts between docs and code, and what it couldn't work out.

## 3. How drafts are reviewed

_Decided: [ADR 0026](../decisions/0013-seed-each-repo-before-its-first-ticket.md) and [0026](../decisions/0013-seed-each-repo-before-its-first-ticket.md)._

### When

Each repo is seeded before its first ticket, and its team reviews the pages as an onboarding task. Before the pilot, that's the repos the first tickets land in. If a ticket lands in a repo with no page, Intake says so and carries on, and a platform engineer seeds that repo before the ticket's plan.

### The PRs

One library PR per service page, opened by the person who ran the skill, labelled `seeding`, with the report as its description. It requests review from the team's tech lead, and the format check runs as on any library PR (design doc 1, sections 6 and 7).

### The review

The team sets time aside, for example one session together. For each page:

1. Read the report first: conflicts between docs and code, and what the skill couldn't work out.
2. Check each section against the lines its report links to.
3. Fix or delete what's wrong, and add what's missing, in the PR.
4. Ask design doc 1's two questions: is anything missing to understand this, and is anything here not needed?
5. Approve. The platform team merges, and the page's `approved_by` names the seeding PR.

If a draft is beyond fixing, or the service is being retired, the team closes the PR, and the service has no page until someone writes it or seeds it again. If drafts are poor across a repo, we improve the skill before the next one.

### Until a page merges

Sessions in that repo start without a service page, and the session-start note says the page is still in review.

## 4. Runbook

### Links to services that don't have a page yet

* **Another of our services without a page.** The "Depends on" section names it in plain words with its full repo name, so it can be linked once it has a page. For example: "ledger-svc (`seeonee/ledger-svc`): records each charge."
* **A third-party service** (a payment provider, say) is named in plain words only. It never gets a library page.

### Who runs it, and what they need

A platform engineer, with someone from the repo's team at hand to confirm the plan. They need:

* Claude Code with the plugin ([design doc 6, section 1](distribution-security-and-cost.md#1-packaging-and-versions))
* their Claude subscription or an Anthropic API key
* clones of the repo and of the library, and their own GitHub access to open library PRs

### Steps for one repo

1. **Book the review** with the team.
2. **Seed.** Pull the repo's default branch, open Claude Code in it and run `seed-service-page`. Confirm the plan.
3. **Glance at each draft.** This isn't the review: it only catches a broken run, such as empty sections or the wrong service. Run it again if needed.
4. **Open the PRs.** A script in the plugin, the same one the library update uses, pushes one branch per page and opens each library PR with the runner's GitHub access.

A new service later on takes the same steps for its repo.

### Usage limits

A run reads a lot of code. On a subscription it counts against the plan's usage limits, so seed repo by repo. With an API key it's ordinary spend ([design doc 6, section 4](distribution-security-and-cost.md#4-spend-limits-and-reporting)).

## What this adds to the harness

| Part | New or reused |
| --- | --- |
| The `seed-service-page` skill | New, in the plugin |
| A read-only subagent that reads code for the skill | New, in the plugin |
| Page format, writing rules, the format check, the review path | Reused from design doc 1 |
| The script that opens library PRs | Reused from the library update |
