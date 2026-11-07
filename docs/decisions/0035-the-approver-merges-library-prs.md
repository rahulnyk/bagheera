---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Whoever approves a library PR merges it

## Context and problem statement

Anyone can approve a library PR with an `/approved library` comment ([0002](0002-anyone-approves-with-a-comment.md)), and agents never merge (PRD K4). Who merges, and what does the library's branch rule require?

## Considered options

* A. The approver merges, straight after approving
* B. The person who opened the PR merges, once it's approved
* C. The platform team merges every library PR

## Decision outcome

Chosen by the owner: A.

* The person who comments `/approved library` merges the PR straight after.
* The library's branch rule: every change goes through a PR, the format check must pass ([0031](0031-the-format-check-in-the-library-repo.md)), and force-pushing is blocked. It requires no GitHub review, since the approval is the comment.
* Approvers need write access to the library repo, which the platform team grants when a team joins.

### Consequences

* Good, because an approval takes one visit, and nobody holds a merging role.
* Good, because no library PR waits on one particular person.
* Bad, because nothing technical stops a merge without an `/approved library` comment; the merged PR shows it, and managers act on it.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
