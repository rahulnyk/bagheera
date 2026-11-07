---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Lay out the library by page type, then domain

## Context and problem statement

The library is one repo for the org ([0005](0005-one-knowledge-library-for-the-org.md)). PRD K1 names the page types: standards, services, decisions and features, plus an index. How should its folders be arranged?

## Considered options

* A. By page type only (`services/`, `decisions/`, …)
* B. By domain first (`payments/services/`, `payments/decisions/`, …), with an org-wide area
* C. By page type, then domain (`services/payments/`, `decisions/payments/`, …), with `org` as the domain for org-wide content

## Decision outcome

Chosen option: C. The owner weighed B's "domain as a knowledge tenant" view and chose C for its clear separation of page types, which also makes the library's own views, such as all features or all decisions, straightforward.

* Every page lives at `<type>/<domain>/<page>.md`. Org-wide content uses the domain `org`. The path is the page's ID.
* Each service belongs to exactly one domain: the team that owns its code. Other domains link to it.
* A feature goes under the domain of the team that delivered it, named after its ticket. A decision that binds more than one domain goes in `decisions/org/`.
* Domain standards may add to org standards but not contradict them.
* Links are written as paths from the library root.

### Consequences

* Good, because page types stay at the top as the PRD lists them, and each domain folder has a clear owning team.
* Good, because every path names its domain, so a domain's pages can be found, or moved, by a script.
* Bad, because a domain's knowledge is spread over four folders, so browsing "everything about payments" means visiting each.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
