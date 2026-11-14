---
status: proposed
date: 2026-11-14
deciders: RN (owner)
---
# A ticket's wiki changes are reviewed with its code, and the portal merges them when the code merges

## Context and problem statement

The org has one wiki ([0008](0008-one-wiki-for-the-org.md)), and nothing runs in CI ([0010](0010-nothing-runs-in-ci-in-the-mvp.md)). How does each feature's knowledge reach the wiki without an extra step or an extra approval?

## Considered options

* A. Build opens one wiki PR, linked from the code PR and reviewed with it; the portal merges it when the code PR merges
* B. A command, run after each merge, opens a wiki PR that someone approves and merges later. That's the step that gets forgotten, plus another approval
* C. The portal writes the wiki itself after each merge, with no review. Unreviewed text would land where every session reads it

## Decision outcome

Proposed: A.

* After the last phase, Build writes the ticket's wiki changes on branch `PAY-412` in the wiki: the service pages it touched, the feature page, and the decisions. It opens the wiki PR and links it from the code PR, with a three-line summary.
* The tech lead reviews it along with the code. Re-running Build refreshes it.
* When the code PR merges, the portal runs the page check and merges the wiki PR. If it can't merge cleanly, the board shows it waiting on the developer, and `/bagheera:build` refreshes it. If the code PR closes unmerged, the portal closes the wiki PR.
* Other wiki changes, such as seeding, standards and hand fixes, are ordinary PRs, merged by whoever approves them. Agents never merge.

### Consequences

* Good, because nobody takes an extra step: the review already happens, and the wiki merge follows the code's.
* Good, because a person reviews every change before it reaches the wiki.
* Bad, because the reviewer has two PRs to look at, and may skip the wiki one.
* Bad, because developers need write access to the wiki.
