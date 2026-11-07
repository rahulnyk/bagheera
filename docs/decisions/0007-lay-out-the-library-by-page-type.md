---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Lay out each library by page type

## Context and problem statement

PRD K1 names the page types: standards, services, decisions and features, plus an index. Each team has its own library ([0005](0005-one-library-per-team.md)). How are its folders arranged?

## Considered options

* A. By page type: `standards/`, `services/`, `decisions/`, `features/`
* B. By page type, then domain: `services/payments/`, `decisions/payments/`
* C. By service first: `payments-api/decisions/`

## Decision outcome

Chosen option: A. With one team per library, a domain folder would always hold one value.

* Four folders, `standards/`, `services/`, `decisions/` and `features/`, plus `index.md` and `checks/` at the root.
* A page's path is its ID. A feature entry is named after its ticket, such as `features/PAY-412.md`.
* Links between pages are written as paths from the library root.

### Consequences

* Good, because page types are at the top, as the PRD lists them, and paths stay short.
* Good, because the library's own views, such as all decisions, are one folder each.
* Bad, because a team with many services finds "everything about payments-api" across four folders.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
