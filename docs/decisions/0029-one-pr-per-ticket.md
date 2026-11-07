---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# One PR per ticket

## Context and problem statement

Each stage writes a document into the ticket's folder (PRD §5), and Build adds tests and code. How does a ticket's work reach the main branch, and when does the library learn from it?

## Considered options

* A. A planning PR, then one PR per phase
* B. A planning PR, then one build PR with the phases as commits
* C. One PR per ticket: the spec, the plan and the code together

## Decision outcome

Chosen by the owner: C.

* **One branch and one PR per ticket,** `PAY-412`, opened as a draft by Intake. Intake adds `spec.md`, Plan adds `plan.md`, and Build adds tests and code.
* **Approvals are comments on that PR,** per document ([0017](0017-the-stage-check-and-overrides.md)). Commits to code don't touch them.
* **Phases are steps within Build,** one at a time ([0020](0020-build-phases-one-at-a-time.md)), each tests first. Build ticks each phase off in the PR's description, not in `plan.md`, so the plan's approval stands. After the last phase, it marks the PR ready for code review.
* **Changes along the way:** re-running Intake or Plan changes its document in the same PR, which then needs approving again. Once the PR has merged, more work is a new ticket.
* **The main branch comes in by merging,** never by rebasing or force-pushing, so nobody else's checkout breaks.
* **The library learns once,** when the PR merges: service pages, decisions and the feature entry together.

### Consequences

* Good, because there's one branch, one PR and one merge per ticket.
* Good, because the library learns from a ticket in one update.
* Bad, because code review comes as one PR per ticket, which can be large.
* Bad, because phases can't ship separately behind their own flags.
* Bad, because decisions reach the library at the end of the ticket, not before Build.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
