---
status: superseded by [0070](0070-knowledge-expires-and-every-change-is-checked-once.md)
date: 2026-10-28
deciders: RN (owner)
---
# Send library findings to page owners as ordinary library PRs

## Context and problem statement

PRD K6 asks for a scheduled check that reports broken links, contradictions, stale service pages and orphan pages. The PRD's success metrics ask that pages are updated within a day of a change and that at least 80% of service pages are verified by a person in the last 90 days. Who handles findings, and how?

## Considered options

* A. One weekly report for the whole library, handled by the platform team
* B. The agent opens a fix PR for each finding, reviewed by the page's owners through the normal library review; the platform team sees a weekly summary
* C. One weekly issue per domain, assigned to the domain's owners

## Decision outcome

Chosen option: B. Stale pages, contradictions, broken links, orphans and service pages due for re-verification each become a library PR to the page's owners. Re-verification PRs open at 75 days, at most five per domain per week. Only the platform team clears planted-instruction false alarms.

### Consequences

* Good, because owners use the review path they already know, and nothing piles up on a two-person platform team.
* Good, because re-verification becomes a quick, normal PR that counts towards the 90-day target.
* Bad, because owners get more PRs; the weekly cap and the summary keep this visible and bounded.

## More information

Detail in [design/knowledge-library.md, section 8](../design/knowledge-library.md#3-trust).

Amended by [0054](0054-weekly-summary-as-a-github-issue.md): the platform team's weekly summary is the library section of the one weekly summary, posted as a GitHub issue.
