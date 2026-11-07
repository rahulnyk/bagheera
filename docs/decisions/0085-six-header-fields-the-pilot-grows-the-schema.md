---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Six header fields to start; the pilot grows the schema

## Context and problem statement

Design doc 1 defines about 29 header fields across the four page types before any page exists ([0015](0015-typed-page-headers.md)), and a fixed body shape per type ([0017](0017-brief-pages-with-fixed-shapes-and-length-limits.md)). The first system ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)) reads only a few of them. Should the page types be defined up front, or from the pilot?

## Considered options

* A. Minimal now, grown by the pilot
* B. As designed in doc 1, all fields from the start
* C. Free-form during the pilot, with the schema written at the exit

## Decision outcome

Chosen by the owner: A. Page types and their metadata are defined from the pilot itself.

* **Six fields to start,** the ones Bagheera reads: `type`, `title`, `updated`, `expires` and `approved_by` on every page, and `repos` on service pages, so session start finds the page.
* **Left to other places:** the path is the page's ID and holds its domain; a feature entry is named after its ticket; a superseded decision gets an `expires` date and a link to the decision that replaces it.
* **Body sections** start from doc 1's templates for each type, within their length limits. The format check requires them, so a page can't lose its context.
* **Grown by the pilot.** A field or section is added only when a pilot page needs it and something reads it, by a PR to the schema in the library repo. At the pilot exit, the schema is fixed for rollout.

### Consequences

* Good, because nothing is filled in that nobody reads, and the format check has little to get wrong.
* Good, because the schema that rolls out is the one real pages needed.
* Bad, because later additions, such as the step-by-step reveal or the dashboard, bring their own fields and need pages updated.

## More information

Amends [0015](0015-typed-page-headers.md) and [0017](0017-brief-pages-with-fixed-shapes-and-length-limits.md) for the first system. Detail in [plan.md, section 1](../plan.md#the-knowledge-library) and [design/knowledge-library.md](../design/knowledge-library.md#2-pages).
