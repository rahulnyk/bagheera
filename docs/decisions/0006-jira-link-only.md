---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Link Jira tickets to the work; don't show the stage in Jira

## Context and problem statement

PRD V5 (should have) asks that Jira reflects stage changes, or at least links to the feature's documents. PRD Q6 asks which.

## Considered options

* A link from the ticket to the feature's PRs and documents
* The stage kept in sync in a Jira field

## Decision outcome

Chosen option: link only, for now. How the link is made is settled in design doc 5.

### Consequences

* Good, because there is no Jira write access or field set-up to maintain.
* Bad, because people who live in Jira must follow the link to see the stage.
