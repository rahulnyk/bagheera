---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Name dependencies that have no page yet, and link them once they do

## Context and problem statement

Domains are seeded one at a time, just before their teams join ([0026](0026-seed-each-domain-just-before-its-teams-join.md)). The joining domain's services will call services in domains not yet seeded. A page's `depends_on` can only hold IDs of pages that exist, because the header check rejects unknown IDs (design doc 1, section 7), and the Architecture stage shows Claude the Interfaces section of each dependency (design doc 1, section 9). How should a seeded page refer to a service that has no page yet?

## Considered options

* A. Name it in plain words now, with its repo and a source; add the link once it has a page
* B. Also seed the services the joining domain calls directly, even in other domains, and ask those teams to review early
* C. Seed every domain in the Foundations phase

## Decision outcome

Chosen option: A. The page's "Depends on" section names the service in plain words, with its full repo name and a source footnote; the header leaves it out, and the seeding report lists it. Once that service has a page, the weekly check (design doc 1, section 8) finds the pages that name it and opens a PR to each, adding the ID to `depends_on`; the page's owners approve it. As an exception, if a joining team relies heavily on a service in another domain, the platform team may seed that service early, with its PR going to that domain's owners.

### Consequences

* Good, because it needs no new parts and keeps the review with teams that are about to use their pages.
* Good, because the gap closes by itself through the weekly check.
* Bad, because until then Claude doesn't see that dependency's interfaces at the Architecture stage, only its name.

## More information

Detail in [design/seeding.md, section 4](../design/seeding.md#4-runbook).
