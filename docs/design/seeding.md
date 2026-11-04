# Seeding

_Status: approved by the owner, 2026-10-29; simplified by the owner, 2026-11-04 ([0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md), [0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md), [0083](../decisions/0083-seed-only-the-repos-tickets-land-in.md)) · Design doc 2 of 6 · [All design docs](README.md)_

**Changes since approval** ([ADR 0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [ADR 0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md), [ADR 0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md)). Where these differ from the rest of the doc, they win:

* Seeded pages carry no footnote on each statement for now. The seeding PR lists the files and commits the skill read, and reviewers check the page against the code.
* Seeded pages get an `expires` date, 90 days after their PR is approved, instead of `last_verified` ([design doc 1, shelf life](knowledge-library.md#shelf-life)).
* After seeding, `/bagheera:update-library` keeps the page current, run on demand in the pilot, rather than an hourly run.
* The seeding PR's description is a short report: the commit read, conflicts between docs and code, and what the skill couldn't work out. The list of where to look (section 2) is guidance inside the skill. There's no DeepWiki-Open fallback: if drafts are poor, we improve the skill or owners write the page ([ADR 0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md)).
* The skill borrows DeepWiki-Open's method, and DeepWiki-Open is never run: it plans before it writes, writes only from the planned files, skips vendored code, build output, lock files and env and secret files, and the PR's description gives the files and line ranges behind each section (section 1, [ADR 0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md)).
* Only the repos tickets land in are seeded, each before its first ticket, not a whole domain before its teams join (section 3, [ADR 0083](../decisions/0083-seed-only-the-repos-tickets-land-in.md)). If a ticket lands in a repo with no page, Intake says so and carries on, and the repo is seeded before the ticket's plan.

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
| **Who runs it** | A platform engineer, once per repo, with someone from the service's team at hand (section 4) |
| **Where** | On their laptop, in a clone of the service's repo |
| **Sign-in** | Their Claude subscription (the run counts against its usage limits) or an Anthropic API key. Nothing else to install. |
| **What it reads** | The repo's code and docs at its current commit, by searching and opening files. What it may cite and where it looks is section 2. |
| **What it writes** | A draft page for each service in the repo, in the library's format: typed header, the five service sections, at most 400 words (design doc 1, section 4) |
| **Trust** | Every statement is inferred, and its footnote names the file and commit it was read from |
| **What happens next** | A library PR for the service's owning team (section 3) |
| **Depends on** | Anthropic for the model; GitHub for the repo and the library PR |

For example:

```markdown
payments-api retries a failed charge up to 3 times over 24 hours.[^s1]

[^s1]: inferred · read from `internal/charge/retry.go` at seeonee/payments-api@a1b2c3d on 2026-11-02 · <link to the file at that commit> · reviewed in library PR #12
```

### Why we don't run DeepWiki-Open

The PRD named DeepWiki-Open for this, and [0009](../decisions/0009-seed-service-pages-with-deepwiki-open.md) first chose it. Checking what it takes to run changed that:

| | DeepWiki-Open | Claude Code with a seeding skill |
| --- | --- | --- |
| Setup on the laptop | Docker with a database, a LiteLLM proxy, DeepWiki and Ollama | Nothing new |
| Sign-in | An Anthropic API key or Bedrock; a Claude subscription can't be used | A Claude subscription or an API key |
| Output | Its own wiki format, which would need converting into ours | Our format, with a source on every statement |
| Coverage | Indexes every file | Reads what it judges relevant; section 2 gives it a fixed list of places to look, and the owners' review catches gaps |
| Upkeep | A third-party tool whose README now points to a hosted "2.0" successor | One skill in our plugin |

### What we borrow from DeepWiki-Open

_Decided by the owner: [ADR 0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md)._

We don't run DeepWiki-Open; our skill borrows its method, from its wiki prompts at commit `f89c59a` (3 Nov 2026, MIT licence).

| Borrowed | In our skill |
| --- | --- |
| It plans the wiki from the file tree and README before writing, listing the files relevant to each page | Pass 1, the plan: the repo's services and, for each section of each page, the files to read (section 2's list says where to look). The person running it confirms the plan |
| Each page is written only from its relevant files | Pass 2, the pages: written from the planned files only. Anything not found is left out and listed as not worked out |
| Vendored code, build output, lock files and env files are skipped | The same filter, so seeding never reads credentials. Repo docs stay in (0025), unlike DeepWiki-Open's default |
| Each section cites its files and line ranges | In the seeding PR's description, linked at the commit read, so reviewers check each section in one click. The page itself carries no sources (0070) |

**Left behind:** its server, web app, embeddings and retrieval, Docker, LiteLLM and Ollama; its wiki format and page structure; diagrams on every page.

**Its text.** We write our own instructions. If any of its prompt text is copied, its MIT notice goes in `third_party/deepwiki-open/` with the commit, as for Spec Kit (design doc 3, section 1). It's adapted once, with no regular review.

### Why not a code map

A parser can turn code into an exact map of files, functions and calls (an abstract syntax tree). That describes the code's structure, not what a service is for, how it's used, or what to watch out for, so on its own it doesn't meet K8. It may later help check that a page's `depends_on` links still match the code, if they drift. That would be one more tool to run, so it waits until there's a need.

### Note on the PRD

K8 asks for "a one-off automated pass over their code", which this meets. PRD §8, §9 (Foundations) and Appendix B named DeepWiki-Open; they now name the seeding skill (applied 2026-11-04, logged in [the design index](README.md#prd-wording-to-update)).

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

## 3. How drafts are reviewed

_Decided: [ADR 0026](../decisions/0026-seed-each-domain-just-before-its-teams-join.md). The review itself follows design doc 1, sections 2, 6 and 8._

### When each domain is seeded

| When | What is seeded |
| --- | --- |
| The Foundations phase (PRD §9) | The pilot team's domain |
| The week before each rollout wave | The domains of the 2–3 teams joining |

Services in domains that haven't joined yet have no page until their turn. Links to them are section 4.

### The PRs

* One library PR per service page, opened by the person who ran the skill, with the seeding report as its description and the label `seeding`.
* The PR's number is then filled into every footnote and into `last_verified`, as in the curation run (design doc 1, section 6).
* The branch rules send it to the owners `domains.yaml` names for the domain, and the checks run as on any library PR.

### The review, as an onboarding task

The team sets time aside before its first feature, for example one session together. For each page:

1. Read the seeding report first: conflicts between docs and code, gaps, and what the skill couldn't work out.
2. Check statements against their sources. Each footnote links to the file at the commit read.
3. Fix or delete what's wrong, and add what's missing. Edits are covered by the reviewer's own approval (design doc 1, section 8). A statement a reviewer adds carries the footnote `owner-approved · added in library PR #12`.
4. Ask design doc 1's two questions: is anything missing to understand this, and is anything here not needed?
5. Approve. The approval verifies every statement left on the page, and the page enters the library.

If a draft is beyond fixing, or the service is being retired, the team closes the PR. The service then has no page until someone writes it or seeds it again.

### Until a page merges

Sessions in that repo start without a service page, and the session-start note says the page is still in review. Open seeding PRs appear in the weekly summary with other library PRs, and one waiting more than five working days is flagged to the domain's owners (design doc 1, section 8). The aim is every page merged before the team's first feature.

### Afterwards

* The knowledge agent's hourly run keeps each page current from merged PRs (design doc 1, section 6).
* A domain's pages are all verified in the same week, so they fall due for re-verification together after 75 days. Design doc 1's cap of five re-verification PRs per domain per week spreads them out; a domain with up to 15 pages still meets the 90-day target.
* The pilot measures how long a page takes to review (estimated at 15–30 minutes), to plan the later waves.

## 4. Runbook

_Decided: [ADR 0027](../decisions/0027-name-unseeded-dependencies-and-link-them-later.md) for links to services without a page. The rest follows from sections 1–3._

### Links to services that don't have a page yet

* **Another of our services without a page.** The "Depends on" section names it in plain words with its full repo name, so it can be found later, and a source footnote. The header's `depends_on` leaves it out, and the seeding report lists it. For example: "ledger-svc (`seeonee/ledger-svc`): records each charge.[^s4]"
* **Once it has a page**, the weekly check (design doc 1, section 8) finds the pages that name it and opens a PR to each, adding its ID to `depends_on`. The page's owners approve it. The check does this rather than the skill, because the ID only exists once the new page has merged.
* **A third-party service** (a payment provider, say) is named in plain words only. It never gets a library page.
* **Exception.** If a joining team relies heavily on a service in another domain, the platform team may seed that service early. Its PR goes to that domain's owners.

### Who runs it, and what they need

A platform engineer runs it, with someone from the joining team at hand to confirm each repo's list of services (section 2). They need:

* Claude Code with the plugin (how it reaches laptops is design doc 6; for the pilot, installed by hand)
* their Claude subscription or an Anthropic API key
* clones of the domain's repos and of the library, and their own GitHub access to open library PRs

### Steps for one domain

1. **Check `domains.yaml`.** The domain, its owners and its repos are listed. Only the platform team changes this file.
2. **Book the review.** Agree the team's review session for the coming week (section 3).
3. **Seed each repo.** Pull its default branch, open Claude Code in it and run `seed-service-page`. Confirm the list of services. The skill writes a draft page and a seeding report for each.
4. **Glance at each draft.** This is not the review: it only catches a broken run, such as empty sections or the wrong service. Run it again if needed.
5. **Open the PRs.** A script in the plugin, the same one the curation run uses (design doc 1, section 6), pushes one branch per page, opens each library PR with the runner's GitHub access, and fills the PR number into the footnotes and `last_verified`.
6. **Follow up.** The weekly summary shows open seeding PRs by domain. After the team's pages merge, the weekly check adds links from earlier pages that named these services.

**A new service later on.** When a team starts a new service after its domain was seeded, the platform team adds its repo to `domains.yaml` and runs the same steps for that repo.

### Time and usage limits

* A run reads a lot of code. On a subscription it counts against the plan's usage limits, so a large domain may need more than one sitting; seed repo by repo. With an API key, it is ordinary spend (spend limits are design doc 6).
* The pilot domain's runs are timed and their usage noted, to plan the later waves.

### If drafts are poor

If reviewers rewrite or delete more than half the statements across a domain's pages, the drafts aren't saving time. We improve the skill before the next domain is seeded, and owners write any page that's beyond fixing ([0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md)).

## What this adds to the harness

| Part | New or reused |
| --- | --- |
| The `seed-service-page` skill | New, in the plugin |
| A read-only subagent that reads code for the skill | New, in the plugin |
| Page format, writing rules, checks, review path | Reused from design doc 1 |
| The script that opens library PRs and fills in PR numbers | Reused from the curation run |
| A "missing link" finding in the weekly check | A new finding in an existing check |
