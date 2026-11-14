---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# One ticket, one repo, one branch, one PR

## Context and problem statement

A ticket's work needs one home that people, the commands and the portal can all find without being told.

## Decision outcome

Chosen by the owner.

* Each Jira ticket is delivered in exactly one repo. Work that spans repos is one ticket per repo, linked in Jira.
* The branch is named after the ticket key and made from the repo's default branch. One PR holds the spec, plan, tests, code and wiki changes. The portal opens it as a draft with the spec's first commit.
* The default branch comes in by merging, never by rebasing or force-pushing.
* Once the PR has merged, more work is a new ticket. To abandon a ticket, close its PR.

### Consequences

* Good, because nothing has to be coordinated across repos or branches.
* Bad, because a PM writes one spec per repo for what may feel like one feature.
