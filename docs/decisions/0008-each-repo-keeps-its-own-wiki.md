---
status: proposed
date: 2026-11-14
deciders: RN (owner)
---
# Each repo keeps its own wiki, updated in the feature's own PR

## Context and problem statement

Where does the wiki live: in one repo for the org, or beside the code in each repo? With nothing in CI ([0010](0010-nothing-runs-in-ci-in-the-mvp.md)), a separate library needs someone to remember an update after every merge, and needs its own PR and approval.

## Considered options

* A. `wiki/` in each code repo, updated by Build's last phase in the ticket's PR; org standards in one standards repo
* B. One library repo for the org, updated by a command after each merge, through its own PR

## Decision outcome

Proposed: A.

* The wiki is `wiki/services/`, `wiki/decisions/` and `wiki/features/<TICKET>/`. Once merged, a feature's spec and plan are its record.
* Build's last phase updates the service pages and writes the decisions. The tech lead reviews them with the code.
* **The code wins.** A session that finds a page wrong fixes it in its own PR. Pages have no expiry dates and no lists of approvers.
* **Branches never conflict.** There's no index file and no shared numbering: the hook and the portal build the index from headers, and decisions are named by slug.
* Spec drafting in the portal reads every onboarded repo's wiki. A Claude Code session reads its own repo's wiki and the standards.
* Org standards live in `seeonee/standards`, owned by the architecture council ([0014](0014-standards-are-numbered-rules-owned-by-the-council.md)).

### Consequences

* Good, because knowledge changes with the code, in one PR, under one review.
* Good, because there's no extra command, PR or approval, so nothing to forget.
* Bad, because a session in one repo sees other repos' wikis only if it opens them. The portal sees them all.
* Bad, because PRs made without Bagheera don't update the wiki until a later session fixes it. CI can close that gap at org adoption.
