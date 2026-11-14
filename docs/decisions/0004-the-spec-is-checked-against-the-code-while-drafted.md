---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# The spec is checked against the code while it's drafted

## Context and problem statement

Specs drafted from Jira and Confluence alone often conflicted with the code. Each conflict surfaced later, as a clarification, a changed spec and another approval.

## Decision outcome

Set by the owner: check while drafting.

* The drafting agent reads the repo's code and wiki as well as the ticket.
* Every statement about today's behaviour is checked and listed in the spec's *Today* section, with the file it was checked in.
* If the code contradicts a statement, it becomes a question for the PM, with the evidence in plain words. It is never silently corrected.

### Consequences

* Good, because a conflict costs one question while the PM is still writing.
* Good, because the PM learns from the spec itself how the product works today.
* Bad, because drafting needs read access to the code, so the portal keeps clones of repos.
