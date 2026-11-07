---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The pilot proves the system works end to end, on at least 10 tickets

## Context and problem statement

PRD §7 sets ten pilot targets for the full design: automated review, the dashboard, PM response times, meeting counts, CI cost and more. The first system ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)) has no Verify, dashboard, CI or test plan, so most can't be measured. G3 asks for 2 features, while §8 and §9 say one. What does the pilot measure?

## Considered options

* A. Rewrite §7 for the pilot: keep the targets the first system can meet, and mark the rest for when their additions arrive
* B. Fix only the conflicts: one feature, and shelf life instead of 90-day verification
* C. Leave §7 as it is

## Decision outcome

Chosen by the owner: A, cut down further. A pilot proves the concept works end to end; it doesn't measure efficiency. That comes once the pilot works.

* **The pilot runs at least 10 tickets,** not one feature.
* **Two targets only:**
  * at least 10 tickets taken through intake, plan and build, each with its spec and plan approved by the named PM and tech lead before Build
  * each of those tickets updates the library, approved by its tech lead or PM
* **Efficiency measures wait until after the pilot:** shelf life, automated review, weekly use, response times, rewrites, meetings, the dashboard question and cost per merged PR. Their targets are set then.
* **Noted along the way, not targets:** overrides and their reasons, what people asked for, each run's cost, and the sandbox's friction, because they decide the additions and the deferred decisions.

### Consequences

* Good, because the pilot has one clear question, does it work end to end, and two things to count.
* Good, because nobody spends the pilot gathering numbers that the first system can't yet move.
* Bad, because the pilot gives no efficiency evidence; rollout starts on proof that it works, not proof that it saves time.

## More information

PRD §7, §8, §9 and Q9 updated on the owner's choice; logged in [the design index](../design/README.md#prd-changes). Detail in [plan.md, section 2](../plan.md#2-the-pilot).
