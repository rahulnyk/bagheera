---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Build the delivery path and the knowledge library first

## Context and problem statement

The six design docs describe the whole of Bagheera. The owner's rule is to build the simplest possible system first, test it, then add to it, not to build every part of the full design in a simple form. What is the first system?

## Considered options

* A. The delivery path only: four commands, with approvals in PRs
* B. The knowledge library only
* C. The delivery path plus Verify
* D. The delivery path plus the knowledge library, in its simplest form

## Decision outcome

Set by the owner: D. The knowledge library is the heart of the harness.

* **The delivery path:** one plugin with `/bagheera` (where a feature stands and the next step), `/bagheera:intake` (the spec and acceptance criteria from the Jira ticket; the PM approves), `/bagheera:plan` (how it will be built; the tech lead approves) and `/bagheera:build` (tests first from the criteria, then the PR). Approvals are `/approved` comments, and a command stops without them. PMs find what's waiting through GitHub's own review requests. The pilot's deny rules file stays.
* **The knowledge library:** plain typed pages in four folders, seeded with `seed-service-page`, loaded at session start, and kept current by `/bagheera:update-library`, with owners approving every change. How it is simplified is [0070](0070-knowledge-expires-and-every-change-is-checked-once.md).
* **Added later, one at a time,** each when the pilot shows it's needed: Verify and the standards hook, the dashboard and the weekly summary, the Jira link comment, the separate architecture, test-plan, demo, close and revise commands, and the rest of the design docs.

### Consequences

* Good, because the first thing the pilot team uses is small enough to build, learn and change quickly.
* Good, because the six design docs stay as the target, and each addition is already designed.
* Bad, because PMs and leadership get no dashboard at first; GitHub's own review requests carry the approvals.
* Neutral: PRD §9's rollout starts with Foundations and pilots stages 1–4 before 5–8; this changes that order, listed for the owner.

## More information

Detail in `docs/plan.md`.
