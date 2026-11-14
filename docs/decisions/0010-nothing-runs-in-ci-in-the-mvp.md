---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Nothing runs in CI in the MVP

## Context and problem statement

Wiki updates after every merge and an automated review of every PR fit CI best. But CI depends on each org's settings, and needs agreement from more stakeholders.

## Decision outcome

Set by the owner: no CI at the start.

* Everything runs in Claude Code on developers' machines, or in the portal.
* Each repo's own CI runs its tests as today.
* Verify and wiki updates in CI are proposed for [org adoption](../proposals/org-adoption.md).

### Consequences

* Good, because a team can start without an org-wide agreement on CI.
* Bad, because until then the wiki misses PRs made without Bagheera, and nothing reviews every PR against the standards.
