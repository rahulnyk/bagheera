---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Seed only the repos tickets land in

## Context and problem statement

Seeding runs once per repo, writing one page per service in it; after that, the library update keeps the pages current. [0026](0026-seed-each-domain-just-before-its-teams-join.md) seeds every repo in a domain the week before its teams join, so the pilot would seed every repo in the pilot team's domain. Which repos get seeded?

## Considered options

* A. Only the repos tickets will be delivered in, before the first ticket there
* B. Every repo in the domain, as in 0026
* C. No seeding; pages come from the library update after a repo's first merged ticket

## Decision outcome

Chosen by the owner: A.

* Before the pilot, a platform engineer seeds the repos the first tickets will land in, and the team reviews those pages.
* Other repos wait until work reaches them. If a ticket lands in a repo with no page, `/bagheera:intake` says so and carries on, and a platform engineer seeds that repo before the ticket's plan.
* Teams joining at rollout are seeded the same way: each repo before its first ticket.

### Consequences

* Good, because owners review only pages that will be used, a few at a time.
* Good, because seeding needs no list of a domain's repos up front.
* Bad, because the first ticket in a new repo waits on seeding and its review, or starts without a page.

## More information

Replaces the timing in [0026](0026-seed-each-domain-just-before-its-teams-join.md); its review as an onboarding task stays. Detail in [plan.md](../plan.md#the-knowledge-library) and [design/seeding.md](../design/seeding.md).
