---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Keep the stage check's override in the first system

## Context and problem statement

[0032](0032-stage-check-stops-and-records-overrides.md) lets a developer go past a stop with `--override "<reason>"`, recorded as a PR comment and listed on the dashboard. The first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)) has no dashboard, and an absent approver can be swapped in the spec's header ([0072](0072-named-pm-and-tech-lead-approve.md)). Is the override still worth having?

## Considered options

* A. No override: a stop is a stop
* B. Keep `--override "<reason>"`, posted as a PR comment
* C. No stops: warn and carry on

## Decision outcome

Chosen by the owner: B, as in 0032.

* The command stops without the approvals it needs. `--override "<reason>"` goes ahead anyway; the reason can't be empty.
* The command posts the override on the PR: the stage, what was missing, the reason and who.
* An override isn't an approval. Nothing in an unapproved document reaches the library as approved work.
* With no dashboard, the platform team counts overrides from the PRs each week during the pilot.

### Consequences

* Good, because nobody is ever stuck, and every skip is on record where the work is.
* Good, because the pilot can see how often approvals were skipped, and why (PRD §9).
* Bad, because overrides are seen only on the PR until the dashboard is added.

## More information

Detail in [plan.md, section 1](../plan.md#the-delivery-path).
