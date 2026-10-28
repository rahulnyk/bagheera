# Knowledge library

_Status: in progress (sections 1–5 of 9) · Design doc 1 of 6 · [All design docs](README.md)_

The knowledge library is the org's single, shared set of markdown pages: standards, one page per service, decisions, and one entry per shipped feature (PRD K1). Every Claude Code session reads it (K7), and every merged change can add to it (K3). This doc says how it is structured, where each piece of knowledge comes from, how it is curated, checked and corrected, and how sessions read it.

Covers PRD K1–K7, K9 and K10. Seeding the first service pages (K8) is [design doc 2](README.md).

## Principles

These apply to every section below.

* **Caution and provenance.** Every statement says where it came from, and nothing external is trusted blindly ([0008](../decisions/0008-caution-and-provenance-for-the-knowledge-library.md)).
* **Brief, but never without context.** Set by the owner on 2026-10-28. We actively fight verbosity, but not at the cost of context. A decision without its context is as good as lost; a decision buried in long reasoning and detailed logs is just as unusable. Each page keeps the essentials and links to the full detail, which stays where it was written (the approved spec, the plan, the PR). How this is enforced is being decided; it will be added to section 4.

## 1. Provenance and trust

_Decided: [ADR 0012](../decisions/0012-record-provenance-per-statement.md), following [0008](../decisions/0008-caution-and-provenance-for-the-knowledge-library.md)._

Every statement in the library can be traced to where it came from, and says how far we trust it.

### Trust levels

| Level | What it covers | Examples | Can it be in the library? |
| --- | --- | --- | --- |
| **Owner-approved** | Written or approved by the named owner of the page or folder | A standard approved by the security owners; a decision record approved by its decision owners | Yes |
| **Approved work** | Material approved through the delivery flow | A spec marked `/approved` by the PM; an architecture marked `/approved` by the tech lead | Yes, with a link to the approval |
| **Inferred** | Worked out by the knowledge agent or DeepWiki-Open from code or approved documents, and not yet checked by a person | "payments-api calls ledger-svc", read from a merged diff | Yes, marked as inferred until a person verifies it. When it may enter without a person is section 2. |
| **External** | Text from outside the approved flow | Ticket descriptions, PR descriptions, pasted or linked documents, chat | Never as a statement. At most a link inside a source note, until a person confirms it. |

A person can **verify** an inferred statement. Its source note then records who verified it and when, and it counts as owner-approved. How verification is requested and tracked is section 8.

### How each statement records its source

**Statements the knowledge agent writes** (service pages, feature entries, proposed decisions) each end with a footnote. The footnote gives the trust level, the source with a link, and the date. A statement is a sentence or list item that states a fact; headings and links need no footnote.

```markdown
payments-api retries a failed charge up to 3 times over 24 hours.[^s1]
payments-api calls ledger-svc to record each charge.[^s2]

[^s1]: approved work · PAY-412 spec, `/approved` by @asha on 2026-10-20 · <link to the approval comment>
[^s2]: inferred · from the diff of seeonee/payments-api#431, merged 2026-10-24 · not yet verified
```

**Pages written by their owners** (standards, and decision records written by people) record provenance once, in the page's front matter: owner, approved by, link to the approval, date. The owner vouches for the whole page, so they need no footnotes.

GitHub renders footnotes, so people see them as numbered references. They are plain Markdown, which keeps the format portable (K10).

### What a check enforces

The checks themselves are section 7. For provenance, a check rejects:

* any statement written by the agent without a footnote
* any footnote whose link doesn't point to a source inside our GitHub org
* any statement whose only source is external

### What it means for Claude

When a session loads a page, Claude sees which statements are settled (owner-approved or approved work) and which are inferred. The session-start instructions tell it to treat inferred statements as hints to confirm in the code, not as facts. Section 9 covers what is loaded.

## 2. Merge rules under caution

_Decided: [ADR 0013](../decisions/0013-merge-only-restated-approved-work-without-a-person.md)._

A change from the knowledge agent enters the library on its own only when a person has already stood behind everything it says.

| The change | Enters the library | Who reviews |
| --- | --- | --- |
| Every new or changed statement cites **approved work** or an **owner-approved** page, and all checks pass | By itself | Nobody; it shows in the change log |
| Any statement is **inferred**, for example read from code | After review | The page's owning team; for a service page, the service's team |
| A new or changed **decision record** | After approval | The decision's owners |
| Any change to a **standard** | After approval | The standard's owners |
| Any check fails, including the injected-instructions screen | Not until fixed | The page's owners (the platform team during the pilot) |
| A statement whose only source is **external** | Never | Nobody; the check rejects it |

**In practice.** Feature entries mostly restate the approved spec, architecture, plan, test plan and demo, so they merge by themselves. Service-page changes read from code get a short review by the service's team. Reviewers see every statement with its source footnote, so they check sources, not prose. When they approve, the inferred statements they approved are marked as verified (section 1).

**One PR never mixes the two kinds.** The knowledge agent puts changes that can merge by themselves and changes that need a person into separate PRs (section 6). How a reviewer's approval is recorded on a library PR is also section 6.

**Effect on the PRD's targets and K4.**

* *Pages updated within a day of a change (≥ 90%):* feature entries meet it on their own; service pages depend on how fast the service's team reviews. Pending library reviews appear on the dashboard and in the weekly report.
* *Pages verified by a person in the last 90 days (≥ 80%):* each review of inferred statements counts as verification, which helps.
* *K4* ("agents may merge service and feature page changes after automated checks") holds in a narrower form: the agent merges only what restates approved work.

**When to revisit.** After the pilot. If reviews turn into rubber stamps, relax to "merge with marks" (inferred statements merge marked, and owners verify later). If anything wrong gets through, tighten to "nothing without a person".

## 3. Structure

_Decided: [ADR 0014](../decisions/0014-lay-out-the-library-by-type-then-domain.md), following [0007](../decisions/0007-one-knowledge-library.md)._

One master library, with pages grouped by type first, then by domain.

```text
domains.yaml                 each domain's owners and the code repos it covers
standards/
  org/                       org-wide standards
  payments/                  additions that apply to one domain only
services/
  payments/payments-api.md
  search/search-api.md
decisions/
  org/                       decisions that bind more than one domain
  payments/0042-retry-limits.md
features/
  payments/PAY-412.md
index.md                     generated by a script
CHANGELOG.md                 generated by a script
```

### Rules

1. Every page lives at `<type>/<domain>/<page>.md`. Org-wide content uses the domain `org`.
2. Each service belongs to exactly one domain: the team that owns its code. Other domains link to it.
3. A feature goes under the domain of the team that delivered it. A decision that binds more than one domain goes in `decisions/org/`.
4. Domain standards may add to org standards but not contradict them. The weekly check flags contradictions (section 8).
5. Links are written as paths from the library root.
6. Every page's header names its domain and a stable ID (section 4).
7. `domains.yaml` lists each domain's owners and the code repos it covers. It decides who reviews a page and which domain a session is in.
8. The harness reads a list of library repos, which starts with one entry.
9. A script, not the knowledge agent, writes `index.md` and `CHANGELOG.md`.

### Splitting off a domain later

If a domain ever needs its own repo, for restricted access or size:

1. `git filter-repo` copies `*/payments/` into a new repo with full history, keeping the same type-then-domain layout.
2. One PR removes those folders from the master.
3. The new repo is added to the harness's list of library repos, and the domain's entry moves from the master's `domains.yaml` to the new repo's.
4. A script turns links that now cross repos into full GitHub links. The domain is part of every path, so it finds them reliably.
5. Org-wide content stays in the master. Sessions load `org/` from the master plus their own domain's repo.

### What this layout means for dashboards

The story dashboard is built from PRs and stage documents in the product repos (PRD V4), not from the library. The type-first layout makes the library's own views straightforward: shipped features, decisions, and pages waiting for review, each one folder, filterable by domain.

## 4. Page format

_Decided: [ADR 0015](../decisions/0015-typed-page-headers.md)._

Every page is a Markdown file with a YAML header. Each page type has its own set of header fields, defined in a schema and validated by a check (section 7). Fields the schema doesn't know are rejected, so typos don't pass silently. The schemas live with the harness's checks, not in the library.

The format stays compatible with the Open Knowledge Format (PRD K10): a YAML header, a required `type`, and plain Markdown below it. Our extra fields sit alongside `type`, so a later export needs no rewrite.

### Fields on every page

| Field | What it holds | Example |
| --- | --- | --- |
| `type` | `standard`, `service`, `decision` or `feature` | `service` |
| `id` | A stable ID, set from the page's path when it is created. It never changes, even if the page later moves. Other pages refer to it. | `services/payments/payments-api` |
| `title` | Human-readable title | `Payments API` |
| `domain` | A domain from `domains.yaml`, or `org` | `payments` |
| `owners` | GitHub teams who own the page | `["@seeonee/payments-tech-leads"]` |
| `status` | Depends on the type; see below | `active` |
| `last_verified` | When a person last verified the page, and who | `{date: 2026-10-20, by: "@kim"}` |

### Fields on owner-written pages

Standards, and decision records written by people, record their provenance once (section 1):

| Field | What it holds |
| --- | --- |
| `approved_by` | Who approved the page |
| `approval` | Link to the approval |

### Fields by page type

| Type | Fields | `status` values |
| --- | --- | --- |
| **Service** | `repos` (code repos), `languages`, `depends_on` (service IDs), `environments` | `active`, `deprecated`, `retired` |
| **Decision** | `date`, `deciders`, `supersedes`, `superseded_by` (decision IDs) | `proposed`, `accepted`, `superseded`, `rejected` |
| **Feature** | `ticket` (Jira key), `repos`, `prs`, `shipped` (date), `decisions` (decision IDs) | `shipped` |
| **Standard** | `applies_to` (languages, services or environments; empty means everywhere), `enforcement` (`blocking` or `advisory`, used by the PR review in design doc 4) | `draft`, `active`, `retired` |

### Example: a service page

```markdown
---
type: service
id: services/payments/payments-api
title: Payments API
domain: payments
owners: ["@seeonee/payments-tech-leads"]
status: active
last_verified: {date: 2026-10-20, by: "@kim"}
repos: [seeonee/payments-api]
languages: [go]
depends_on: [services/payments/ledger-svc]
environments: [dev, stage, prod]
---
# Payments API

Charges cards and retries failed charges.[^s1]

[^s1]: approved work · PAY-412 spec, `/approved` by @asha on 2026-10-20 · <link>
```

### `domains.yaml`

One entry per domain, plus `org`:

```yaml
payments:
  owners: ["@seeonee/payments-tech-leads"]
  product_owners: ["@seeonee/payments-pms"]
  repos: [seeonee/payments-api, seeonee/payments-web, seeonee/ledger-svc]
org:
  owners: ["@seeonee/architecture"]
  product_owners: ["@seeonee/product-leads"]
```

`owners` review technical pages and decisions; `product_owners` review product decisions (section 5). `repos` tells a session which domain it is in (section 9).

## 5. What each stage adds

_Decided: [ADR 0016](../decisions/0016-learn-decisions-when-approved-features-at-close.md)._

The library learns from a feature at three moments: when a decision is approved, when code merges, and when the feature ships. Work in progress stays in the product repo's feature folder (`specs/<TICKET>/`) and on the dashboard.

| Stage | What the library gets | Who approves it |
| --- | --- | --- |
| Intake | Proposed product decisions: only the PM's answers that set a rule beyond this feature | The domain's `product_owners` |
| Architecture | Proposed technical decisions from the approved architecture | The domain's `owners`; `decisions/org/` and org owners if more than one domain is bound |
| Plan, Test plan | Nothing yet | — |
| Build (each phase PR) | Service page updates read from the merged code, and the lessons the engineer recorded | The service's team |
| Verify, Demo | Nothing yet | — |
| Close | The feature entry, built from the approved documents and the list of merged PRs | Merges by itself when every statement restates approved work (section 2) |
| Any other merged PR (bug fix, refactor) | Service page updates read from the code | The service's team |
| Standards | Changes only by their owners; the agent may propose, for example after the weekly check | The standard's owners |

### Rules

1. A PM's answer becomes a product decision only if it sets a rule beyond this feature. Answers specific to the feature reach the library through the feature entry at Close.
2. If the build changes an approved decision, the agent proposes a new decision record that replaces the old one (`supersedes` and `superseded_by`, section 4). Its owners approve it again.
3. Engineers record lessons and gotchas in the feature folder during the session, through the knowledge skill. They reach the service page when the PR merges. They are treated as inferred, so the service's team reviews them.
4. The feature entry is written once, at Close, only from documents marked `/approved` and the list of merged PRs. How Close is detected (demo approved and the last phase merged) is settled in design doc 3.

### Note on the PRD

PRD §5 adds "accepted decisions" to the library at Close. Proposing them when their stage is approved matches K3 ("on every merged PR … proposes new decisions") and gets decisions to other teams weeks earlier.

## 6. The curation run

_To brainstorm._

## 7. Checks

_To brainstorm._

## 8. Corrections

_To brainstorm._

## 9. How sessions read it

_To brainstorm._
