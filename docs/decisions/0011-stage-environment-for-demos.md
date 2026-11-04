---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Run demos on the existing stage environment

## Context and problem statement

The Demo stage runs the acceptance criteria on a preview environment so the PM can accept without a meeting (PRD C6, should have). PRD Q4 asks whether services have per-PR preview environments. Most orgs have dev and stage environments instead.

## Considered options

* Per-PR preview environments
* The existing stage environment

## Decision outcome

Chosen option: the stage environment is the main preview environment for demos.

### Consequences

* Good, because nothing new has to be built; existing CD already deploys there (deploying stays a non-goal).
* Bad, because stage is shared, so a demo runs after the feature's phases reach stage, and other changes may be there at the same time.
