---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Build a ticket's phases one at a time

## Context and problem statement

The plan splits a ticket into 2–6 phases, all built in the ticket's one PR ([0029](0029-one-pr-per-ticket.md)). Some phases don't depend on each other. Should they be built in parallel?

## Decision outcome

Set by the owner: no. Within a ticket, phases are built one at a time, in order, even when they're independent. The owner's reason: the harness is used by people, and cognitive simplicity is a necessity for adoption.

* `/bagheera:build` takes no phase number. It builds the next phase, or carries on with the open one.

### Consequences

* Good, because everyone knows which phase is next.
* Good, because the command has no argument.
* Bad, because a ticket whose phases could run side by side takes longer. Separate tickets can still be worked in parallel.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
