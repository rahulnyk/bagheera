---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# One PR per ticket

## Context and problem statement

[0030](0030-one-planning-pr-per-feature.md) gives each feature a planning PR, then one PR per phase, with four written merge rules and a feature-folder check in CI. [0040](0040-build-phases-one-at-a-time.md) builds the phases one at a time. The first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)) has no CI. How does a feature's work reach the main branch?

## Considered options

* A. A planning PR, then one PR per phase, one at a time; no folder check or written rules
* B. A planning PR, then one build PR with the phases as commits
* C. One PR per ticket: the spec, the plan and the code together

## Decision outcome

Chosen by the owner: C.

* **One branch and one PR per ticket,** `PAY-412`, opened as a draft by Intake. Intake adds `spec.md`, Plan adds `plan.md`, and Build adds tests and code.
* **Approvals are comments on that PR,** per document ([0072](0072-named-pm-and-tech-lead-approve.md), [0074](0074-any-change-cancels-an-approval.md)). Commits to code don't touch them.
* **Phases are steps within Build,** one at a time, each tests first. Build ticks each phase off in the PR's description, not in `plan.md`, so the plan's approval stands. After the last phase, it marks the PR ready for code review.
* **Changes along the way:** re-running Intake or Plan changes its document in the same PR, and its approval is needed again. Once the PR has merged, more work is a new ticket. How demo feedback is handled is settled when Demo is added.
* **Dropped:** the planning PR, phase PRs, the four merge rules, the feature-folder check, and the "previous phase merged" check. Phase PRs come back only if ticket PRs prove too large to review.

### Consequences

* Good, because there's one branch, one PR and one merge per ticket, and the stage check loses two conditions.
* Good, because the library learns from a ticket once, when its PR merges: service pages, decisions and the feature entry together.
* Bad, because code review comes as one PR per ticket, which can be large.
* Bad, because phases can't ship separately behind their own flags.
* Bad, because decisions reach the library at the end of the ticket, not before Build.
* Neutral: PRD §5's "one PR per phase" and "each shippable on its own behind a flag" change; applied to the PRD on the owner's choice.

## More information

Replaces [0030](0030-one-planning-pr-per-feature.md). Amends [0040](0040-build-phases-one-at-a-time.md): phases stay one at a time, within the PR. Settles the first system's part of [0036](0036-revise-with-one-command-minor-or-new-ticket.md). Detail in [plan.md, section 1](../plan.md#the-delivery-path).
