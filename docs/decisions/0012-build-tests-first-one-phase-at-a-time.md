---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Build tests first, one phase at a time, with criteria IDs in test names

## Context and problem statement

How does Build prove each acceptance criterion, and in what order does it work through the phases?

## Decision outcome

Chosen by the owner.

* Each criterion has an ID, such as `PAY-412-AC-3`, which is never reused or changed. In test names it's written `PAY_412_AC_3`, which every common test framework accepts.
* `/bagheera:build` builds the next phase, or carries on with the open one. Phases go one at a time and in order, even when they're independent, because simplicity matters for adoption.
* For each phase, it writes tests for its criteria, checks that they fail for the right reason, and commits them on their own. Then it writes code until they pass.
* Build ticks off the phase in `plan.md`, with each criterion's tests and result, which the portal shows the PM.
* Build never changes the criteria. A wrong criterion becomes a question for the PM.

### Consequences

* Good, because the PM can see which criteria pass.
* Bad, because independent phases take longer one after another.
