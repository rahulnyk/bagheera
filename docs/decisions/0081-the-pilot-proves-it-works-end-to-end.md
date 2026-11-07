---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The pilot proves the system works end to end, on at least 10 tickets

## Context and problem statement

The MVP ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)) has no automated review, dashboard or CI, so most of the PRD's efficiency measures can't be taken in the pilot. What does the pilot measure?

## Considered options

* A. Prove it works end to end on at least 10 tickets, with two targets; measure efficiency after the pilot
* B. Targets for every goal the MVP touches, including response times and cost per merged PR

## Decision outcome

Chosen by the owner: A. A pilot proves the concept works end to end; it doesn't measure efficiency. That comes once the pilot works.

* **At least 10 tickets,** from one team.
* **Two targets:**
  * at least 10 tickets taken through intake, plan and build, each with its spec and plan approved by the PM and tech lead before Build
  * each of those tickets updates the library, approved by its tech lead or PM
* **Efficiency measures wait until after the pilot:** shelf life, weekly use, response times, rewrites, meetings and cost per merged PR. Their targets are set then.
* **Noted along the way, not targets:** approvals and who gave them, what people asked for, each run's cost, and the sandbox's friction, because they decide what's added next.

### Consequences

* Good, because the pilot has one clear question, does it work end to end, and two things to count.
* Good, because nobody spends the pilot gathering numbers the MVP can't yet move.
* Bad, because the pilot gives no efficiency evidence: other teams adopt on proof that it works, not proof that it saves time.

## More information

Detail in [plan.md](../plan.md).
