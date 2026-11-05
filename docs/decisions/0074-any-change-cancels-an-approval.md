---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Any change to a document cancels its approval

## Context and problem statement

[0039](0039-wording-fixes-keep-an-approval.md) lets a wording fix keep an approval when its commit carries the trailer `Bagheera-Change: wording`. Claude adds the trailer itself, people add it by hand, and the stage check reads commit trailers. For the first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)), is that worth it?

## Considered options

* A. Any change cancels the approval; no trailer
* B. Keep the wording-fix trailer
* C. Approvals never cancel

## Decision outcome

Chosen by the owner: A.

* An approval counts only if it's newer than the last commit that changed the document.
* After any change, typo fixes included, the approver comments `/approved` again.

### Consequences

* Good, because the check is one comparison of dates, and there's no mark for people to learn or misuse.
* Good, because what was approved is always exactly what's in the document.
* Bad, because approvers are asked again for small fixes; it's one comment.

## More information

Replaces [0039](0039-wording-fixes-keep-an-approval.md). Detail in [plan.md, section 1](../plan.md#the-delivery-path).
