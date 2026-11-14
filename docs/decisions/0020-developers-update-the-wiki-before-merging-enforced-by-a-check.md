---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Developers update the wiki with a command before merging, and a required check enforces it

## Context and problem statement

The org has one wiki ([0008](0008-one-wiki-for-the-org.md)), and nothing runs in CI ([0010](0010-nothing-runs-in-ci-in-the-mvp.md)). How does each feature's knowledge reliably reach the wiki?

## Considered options

* A. Build opens the wiki PR at the end of its last phase
* B. A command, run after each merge, opens a wiki PR that someone approves later
* C. The portal writes the wiki itself after each merge, with no review
* D. A command the developer runs last, before merging, enforced by a required check

## Decision outcome

Set by the owner: D.

* **The command.** `/bagheera:update-wiki` writes the ticket's wiki changes on branch `PAY-412` in the wiki: the service pages the change touched, the feature page, and the decisions. It opens or refreshes the wiki PR, records the code commit it was written from, links it from the code PR, and marks the code PR ready.
* **Enforced.** The portal posts a check, `bagheera/wiki`, on every PR in onboarded repos. On a ticket's PR it passes once the wiki PR was written from the PR's latest commit, passes the page check and can merge cleanly. Other PRs pass straight away. Each repo's branch rule requires the check, from the portal's GitHub App only.
* **On the final code.** If review changes the code, the check fails until the developer runs the command again.
* **Reviewed with the code.** The tech lead reviews the wiki PR with the code PR. When the code PR merges, the portal merges the wiki PR. Agents never merge.
* **Not CI.** The portal posts the check; nothing runs in the repo's workflows. A CI workflow in the command's place is proposed for [org adoption](../proposals/org-adoption.md).

### Consequences

* Good, because no ticket merges without its knowledge in the wiki.
* Good, because the wiki is written from the code as it merges, after review.
* Bad, because a late fix to the code means running the command again.
* Bad, because merges in onboarded repos wait while the portal is down; repo admins can bypass the check.
* Bad, because developers need write access to the wiki.
