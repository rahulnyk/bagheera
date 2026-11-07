---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Every rule in a standard gets a fixed number

## Context and problem statement

Standards are library pages with one line per rule, each with a reason and an example ([0009](0009-fixed-page-shapes-and-length-limits.md)). People and Claude need to say which rule a change breaks, and to keep pointing at the same rule after its wording changes. How is a rule named?

## Considered options

* A. Each page has a short prefix, and its rules are numbered, never reused or renumbered
* B. No names; references quote the rule's text
* C. One sequence of numbers across all standards

## Decision outcome

Chosen option: A.

* Each standard's rules share a short prefix, unique in the library, such as `sec`, `go` or `pay-api`, and are numbered `<prefix>-<n>`: `sec-1`, `sec-2`…
* IDs are lower case, so they can't be mistaken for Jira keys such as `PAY-412`, or turned into wrong links by Jira autolinks.
* A number is never reused or changed. A rule that's withdrawn stays on the page, marked retired, so old references still point somewhere.
* The format check fails a rule line that doesn't start with its ID, a prefix another standard already uses, and a rule ID that disappears instead of being marked retired.

### Consequences

* Good, because references point at a stable rule even after its wording changes.
* Good, because rule IDs work like the criteria IDs people already know.
* Bad, because retired rules stay on their pages.

## More information

Detail in [design/standards-and-review.md](../design/standards-and-review.md).
