---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# The MVP is the delivery path and the knowledge library, run from six laptop commands

## Context and problem statement

The owner's rule is to build the smallest system that's useful on its own, test it, then add parts one at a time when real use shows they're needed. What is the MVP, the system any team can adopt by itself?

## Considered options

* A. The delivery path only: four commands, with approvals in the ticket's PR
* B. The delivery path and the knowledge library: six commands and the seeding skill
* C. B, plus `/bagheera:verify`, the automated PR review
* D. C, plus `/bagheera:update-dashboard` and a team-level dashboard

## Decision outcome

Chosen by the owner: B. The knowledge library is the heart of the harness.

* **The delivery path:** `/bagheera` (where a feature stands and the next step), `/bagheera:intake` (the spec and acceptance criteria from the Jira ticket), `/bagheera:plan` (how it will be built) and `/bagheera:build` (tests first from the criteria, then code), all in the ticket's one PR, approved with `/approved` comments.
* **The knowledge library:** typed pages, drafted for each repo by the `seed-service-page` skill, read at session start, kept current by `/bagheera:update-library`, with standards written through `/bagheera:standards`.
* **All on laptops.** Every command runs in Claude Code with the person's own access. Nothing runs in CI.
* **Verify** is the first addition, which a team can make alone, once code reviewers find standards or security problems by hand.
* **The dashboard** serves leadership across teams, so it belongs to the org-adoption proposal. PMs find what's waiting on them in GitHub's review requests.
* **The MVP's boundary.** Anything that needs org admins or coordination across teams is an L requirement in the PRD, met by the [org-adoption proposal](../proposals/org-adoption.md). A team adopts the MVP with nothing from org admins.

### Consequences

* Good, because what the pilot team uses is small enough to build, learn and change quickly.
* Good, because the pilot answers one question: do the path and the library work end to end?
* Bad, because PRD R3, an automated review of every PR, waits for Verify.
* Bad, because PMs and leadership have no dashboard.

## More information

Detail in [plan.md](../plan.md) and the [org-adoption proposal](../proposals/org-adoption.md).
