---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The PM and tech lead named in the spec approve; no team fallback

## Context and problem statement

[0050](0050-named-people-per-story-team-fallback.md) names a PM, a tech lead and QA in each spec's header, and lets anyone in the role's team approve as a fallback, with the teams taken from `domains.yaml`. The first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)) has no `domains.yaml`, so the stage check needs another way to know whose `/approved` counts.

## Considered options

* A. Named people only: one PM and one tech lead in the spec's header
* B. Each pilot repo's PM and tech lead teams in one small file; any member's approval counts
* C. No roles: anyone who didn't write the document can approve it

## Decision outcome

Chosen by the owner: A.

* The spec's header names one PM and one tech lead. Intake suggests them and the developer confirms.
* Only their `/approved` counts. Each command requests its approver as a reviewer on the planning PR, so GitHub notifies them.
* To swap someone, for a holiday or a handover, the developer edits the header, and the new person approves.
* No QA name and no team fallback. They come back only if the pilot or rollout shows they're needed.

### Consequences

* Good, because there's no config file and no team lookup: the check compares the commenter with the header.
* Good, because PRD C3's "designated role" is still checked.
* Bad, because an absent approver holds the story until the developer names someone else.
* Bad, because the developer chooses the names; a wrong choice is visible in the header and on the PR.

## More information

Amends [0050](0050-named-people-per-story-team-fallback.md) for the first system. Detail in [plan.md, section 1](../plan.md#the-delivery-path).
