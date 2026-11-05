---
status: superseded by [0070](0070-knowledge-expires-and-every-change-is-checked-once.md)
date: 2026-10-27
deciders: RN (owner)
---
# Let agent changes enter the library by themselves only when they restate approved work

## Context and problem statement

PRD K4 lets agents merge service and feature page changes after automated checks. The owner chose caution ([0008](0008-caution-and-provenance-for-the-knowledge-library.md)), and every statement now carries its source and trust level ([0012](0012-record-provenance-per-statement.md)). What may enter the library without a person?

## Considered options

* A. Nothing: every agent change waits for the page's owner
* B. Only changes where every statement cites approved work or an owner-approved page; anything inferred waits for the page's owning team
* C. Everything that passes the checks, with inferred statements marked for owners to verify later

## Decision outcome

Chosen option: B, for the pilot. Decisions and standards always need their owners, and anything with an external-only source is rejected.

### Consequences

* Good, because a person has stood behind everything that enters the library, but nobody re-approves what a PM or tech lead already approved.
* Good, because reviewing inferred statements counts towards the 90-day verification target.
* Bad, because service-page updates wait for the service's team, so the "updated within a day" target depends on their review speed.

## More information

Revisit after the pilot: relax to C if reviews become rubber stamps, tighten to A if anything wrong gets through. Detail in [design/knowledge-library.md, section 2](../design/knowledge-library.md#6-approving-library-prs).
