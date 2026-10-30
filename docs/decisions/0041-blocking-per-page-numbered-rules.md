---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Blocking or advisory per standard page; every rule gets a fixed number

## Context and problem statement

Standards are owner-written library pages with a header saying where they apply and whether they are `blocking` or `advisory` ([0015](0015-typed-page-headers.md)), and one line per rule with a reason and an example ([0017](0017-brief-pages-with-fixed-shapes-and-length-limits.md)). The PR review must say which rule a change breaks, and PRD R6 wants violations reported by team and by rule. How fine-grained is blocking, and how is a rule named?

## Considered options

* A. Blocking or advisory per page; each page has a short prefix and its rules are numbered, never reused or renumbered
* B. Blocking or advisory per rule, each rule with its own marker and ID
* C. Per page, with no rule IDs; findings quote the rule's text

## Decision outcome

Chosen option: A. A page is blocking or advisory as a whole; when owners want both, they split the page. Each standard page has a `prefix`, unique across the library, and its rules are numbered `<prefix>-<n>`, such as `sec-3`. IDs are written in lower case so they can't be mistaken for Jira keys such as `PAY-412`, or picked up by Jira autolinks. A number is never reused or changed; a dropped rule stays on the page, marked retired. Findings cite the rule ID, and reporting counts by rule ID and team.

Each standard page names its owning team in `owners`, which can differ from the domain's owners (for example, the security team owns security standards). The owners file GitHub uses is generated from `domains.yaml` and each standard page's `owners`.

### Consequences

* Good, because each page answers one question, "does this block?", and rule IDs work like the criteria IDs people already know.
* Good, because findings and reports point at a stable rule even after its wording changes.
* Bad, because a topic with both kinds of rules becomes two pages.

## More information

Detail in [design/standards-and-review.md, section 1](../design/standards-and-review.md#1-rules-within-a-standard). Adds the `prefix` field to design doc 1's standard header.

Amended by [0047](0047-architecture-council-owns-standards-prefixes-deny-list.md): the architecture council assigns prefixes and records them in `prefixes.yaml` in the library.
