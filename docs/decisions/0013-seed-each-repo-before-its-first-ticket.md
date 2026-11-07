---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Seed each repo just before its first ticket; the team reviews the pages as an onboarding task

## Context and problem statement

Seeding writes one draft page per service in a repo, each as its own library PR for the team to review ([0011](0011-seed-service-pages-with-our-own-skill.md)). After that, the library update keeps the pages current. Which repos are seeded, and when?

## Considered options

* A. Only the repos tickets will land in, each just before its first ticket
* B. Every repo the team owns, at once
* C. No seeding; pages come from the library update after a repo's first merged ticket

## Decision outcome

Chosen by the owner: A.

* An engineer on the team seeds a repo before the first ticket lands in it.
* The team reviews that repo's pages together, as an onboarding task with time set aside.
* If a ticket lands in a repo with no page, `/bagheera:intake` says so and carries on, and the repo is seeded before the ticket's plan.

### Consequences

* Good, because the team reviews only pages it is about to rely on, a few at a time.
* Good, because seeding needs no list of repos up front.
* Bad, because the first ticket in a new repo waits on seeding and its review, or starts without a page.

## More information

Detail in [design/seeding.md](../design/seeding.md).
