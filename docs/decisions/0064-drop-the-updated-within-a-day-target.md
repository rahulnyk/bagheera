---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Drop the "updated within a day" target

## Context and problem statement

PRD §7 set a pilot target for G1: at least 90% of pilot service pages updated within a day of a merge that changes them. In the pilot, managers run the library update on demand, every few days ([0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md)), so the pilot couldn't meet it. Keep the target and run the update daily, relax it, or drop it?

## Considered options

* A. Keep it, and run the library update every working day in the pilot
* B. Relax it, for example to within three working days
* C. Drop it

## Decision outcome

Set by the owner: C. The owner judges it a vanity metric. It is removed from PRD §7. G1 keeps its other measure: service pages verified by a person in the last 90 days.

### Consequences

* Good, because the pilot can run the library update every few days without missing a target.
* Neutral: the hourly curation run at rollout stays as designed; it no longer has a target to meet.
* Neutral: earlier records that mention the target ([0008](0008-caution-and-provenance-for-the-knowledge-library.md), [0013](0013-merge-only-restated-approved-work-without-a-person.md), [0020](0020-send-findings-to-owners-as-library-prs.md), [0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md)) stay as written; this record supersedes those mentions.
