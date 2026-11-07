---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Whoever approves a library PR merges it

## Context and problem statement

Anyone can approve a library PR with an `/approved library` comment ([0002](0002-soft-approval-by-comment.md)), and agents never merge (PRD K4). Who merges, and what does the library's branch rule require?

## Considered options

* A. The approver merges, straight after approving
* B. The person who opened the PR merges, once it's approved
* C. The team's tech lead merges every library PR

## Decision outcome

Chosen by the owner: A.

* The person who comments `/approved library` merges the PR straight after.
* The library's branch rule: every change goes through a PR, the format check must pass ([0079](0079-the-format-check-lives-in-the-library-repo.md)), and force-pushing is blocked. It requires no GitHub review, since the approval is the comment.
* Approvers need write access to the team's library repo, which the team grants itself.

### Consequences

* Good, because an approval takes one visit, and nobody holds a merging role.
* Good, because no library PR waits on one particular person.
* Bad, because nothing technical stops a merge without an `/approved library` comment; the merged PR shows it, and managers act on it.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
