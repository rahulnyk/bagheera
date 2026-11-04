---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Build a ticket's phases one at a time

## Context and problem statement

The plan splits a ticket into 2–6 phases, each with its own PR. Until now, a phase waited for the previous one only if it needed that phase's code, so independent phases could be built in parallel. Should they be?

## Decision outcome

Set by the owner: no. Within a ticket, phases are built one at a time, in order, even when they're independent. The owner's reason: the harness is used by people, and cognitive simplicity is a necessity for adoption.

* `/bagheera:build` takes no phase number. It builds the next phase, or carries on with the open one.
* The stage check for Build also needs the previous phase merged. Like every stop, a developer can override it on purpose, and the override is recorded.

### Consequences

* Good, because each ticket has at most one open build PR, and everyone knows which phase is next.
* Good, because the command loses an argument.
* Bad, because a ticket whose phases could run side by side takes longer. Separate tickets can still be worked in parallel.

## More information

Detail in [design/developer-workflow.md, sections 3 and 6](../design/developer-workflow.md#6-build). Amends [0029](0029-stage-commands-under-the-bagheera-plugin.md) (the build command's argument), [0030](0030-one-planning-pr-per-feature.md) (rule 2) and [0034](0034-build-tests-first-and-show-criteria-results.md).

Amended by [0075](0075-one-pr-per-ticket.md): phases are built one at a time within the ticket's one PR, so there is no "previous phase merged" check.
