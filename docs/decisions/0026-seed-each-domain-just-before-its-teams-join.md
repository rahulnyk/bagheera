---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Seed each domain just before its teams join, and review as part of onboarding

## Context and problem statement

Each seeded service page is an inferred change, so it goes to the service's team as its own library PR, and stays out of the library until they approve it (design doc 1, sections 2 and 6). Seeding a domain could put 10–15 pages in front of one team at once. How should seeded drafts reach their owners?

## Considered options

* A. All of a domain's pages at once, timed with that domain's teams joining the harness; reviewing them is an onboarding task with time set aside
* B. A trickle: a few PRs per domain at a time, the next opening when one merges
* C. One PR per domain holding all its pages

## Decision outcome

Chosen option: A. A domain is seeded the week before its teams join: the pilot team's domain in the Foundations phase, then each rollout wave's domains just ahead of the wave (PRD §9). Each service page is its own library PR to the service's team, and the team reviews them together as an onboarding task.

### Consequences

* Good, because the team reviews when it has a reason to care: it is about to rely on these pages.
* Good, because the load is large but planned and one-off, and needs nothing new.
* Good, because it follows the rollout of 2–3 teams at a time, and keeps design doc 1's one PR per page.
* Bad, because services in domains that haven't joined yet have no page, so links to them wait (design doc 2, section 4).
* Bad, because a domain's pages are all verified in the same week, so they fall due for re-verification together; design doc 1's cap of five re-verification PRs per domain per week spreads them out.

## More information

Detail in [design/seeding.md, section 3](../design/seeding.md#3-how-drafts-are-reviewed).

Amended by [0083](0083-seed-only-the-repos-tickets-land-in.md): only the repos tickets land in are seeded, each before its first ticket; the review as an onboarding task stays.
