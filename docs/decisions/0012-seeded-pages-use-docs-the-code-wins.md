---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Seeded pages draw on the repo's docs as well as its code; the code wins

## Context and problem statement

Besides code, repos hold their own docs: a README, a `docs/` folder, design notes, runbooks. They explain purpose and gotchas better than code does, but they are often out of date. What may the seeding skill ([0011](0011-seed-service-pages-with-our-own-skill.md)) read?

## Considered options

* A. Code only; the docs are ignored
* B. Code first, and the docs too; when a doc and the code disagree, the page follows the code and the PR lists the conflict
* C. Docs and code equally

## Decision outcome

Chosen option: B.

* The skill reads the service's code, config and docs.
* When a doc and the code disagree, the page follows the code, and the seeding PR lists the conflict for the team to settle.
* Commit messages, PR descriptions and tickets aren't read, and links out of our GitHub org aren't followed.

### Consequences

* Good, because Purpose and Gotchas get the context that only docs hold.
* Good, because a stale doc can't overrule the code, and reviewers see each conflict without having to find it.
* Bad, because a stale doc statement the code can't confirm or contradict, such as a "why", can still reach a draft; the team's review is the guard.

## More information

Detail in [design/seeding.md](../design/seeding.md).
