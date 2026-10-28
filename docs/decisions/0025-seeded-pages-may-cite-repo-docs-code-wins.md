---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Seeded pages may cite the repo's docs; the code wins on conflict

## Context and problem statement

The seeding skill ([0024](0024-seed-service-pages-with-a-claude-code-skill.md)) drafts a service page from a repo. Besides code, repos hold their own docs: a README, a `docs/` folder, design notes, runbooks. The docs explain purpose and gotchas better than code does, but they are often out of date. Under caution and provenance ([0008](0008-caution-and-provenance-for-the-knowledge-library.md)), what may a seeded statement cite?

## Considered options

* A. Code only; the docs are ignored
* B. Code first; the docs may be cited too, and when a doc and the code disagree the page follows the code and the PR lists the conflict
* C. Docs and code equally

## Decision outcome

Chosen option: B. Statements may cite files in the service's repos, code or docs. All are marked inferred and verified by the owners' review. When a doc and the code disagree, the page follows the code, and the library PR lists the conflict for the owner to settle. Commit messages and PR or ticket descriptions are not cited (they are external, design doc 1 section 1), and links out of our GitHub org are not followed.

### Consequences

* Good, because Purpose and Gotchas get the context that only docs hold.
* Good, because a stale doc can't overrule the code, and the reviewer sees each conflict instead of having to find it.
* Good, because it matches design doc 1: anything read from merged files in our repos is inferred.
* Bad, because a stale doc statement the code can't confirm or contradict (for example, a "why") can still reach a draft; the owners' review is the guard.

## More information

Detail in [design/seeding.md, section 2](../design/seeding.md#2-what-the-skill-reads-and-writes).
