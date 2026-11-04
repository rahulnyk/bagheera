---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Lay out the library by page type, then domain

## Context and problem statement

The library is one master repo that may need a hierarchy or a split later ([0007](0007-one-knowledge-library.md)). PRD K1 names the page types: standards, services, decisions, features, an index and a change log. How should folders be arranged?

## Considered options

* A. By page type only (`services/`, `decisions/`, …)
* B. By domain first (`payments/services/`, `payments/decisions/`, …), with an org-wide area
* C. By page type, then domain (`services/payments/`, `decisions/payments/`, …), with `org` as the domain for org-wide content

## Decision outcome

Chosen option: C. The owner weighed B's "domain as a knowledge tenant" view and chose C for its clear separation of page types, which also makes the library's own views (all features, all decisions) straightforward. Splitting a domain off later stays a scripted job because every path and page header names its domain, links are written from the library root, and the harness reads a list of library repos.

### Consequences

* Good, because page types stay at the top as the PRD lists them, and each domain folder has clear owners.
* Good, because a domain can later move to its own repo with `git filter-repo` and a link-fixing script.
* Bad, because a domain's knowledge is spread over four folders rather than one, so browsing "everything about payments" means visiting each.

## More information

Rules and the split procedure: [design/knowledge-library.md, section 3](../design/knowledge-library.md#3-structure).
