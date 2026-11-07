---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# The newest page wins, contradictions are flagged when used, and each page records its approvers

## Context and problem statement

Pages can disagree, and nothing looks for contradictions on a timer ([0070](0070-knowledge-expires-and-every-change-is-checked-once.md)). People also need to know who stands behind what a page says.

## Considered options

* A. The newer page wins and contradictions are flagged when used; each page records who approved it
* B. A scheduled check that finds contradictions and opens PRs to fix them

## Decision outcome

Set by the owner: A.

* **The newest wins.** When two pages disagree, the one with the later `updated` date is taken as more likely right.
* **Flagged when used.** A session or command that relies on pages that disagree says so, names both, and goes with the newer. `/bagheera:update-library` also notes in its PR any page its changes contradict, so it can be fixed.
* **Expired pages lose.** A contradicting page past its `expires` date is ignored, without a flag.
* **Approvers recorded.** Every page's `approved_by` names who approved the work it came from, such as the `/approved` comments on the spec and the plan, and the library PR that brought it in, whose `/approved library` comment names its approver ([0002](0002-soft-approval-by-comment.md)). Each change replaces the list with the approvals behind that change.

### Consequences

* Good, because nothing runs on a timer, and contradictions surface exactly where they matter.
* Good, because every page says who stands behind it.
* Bad, because a contradiction nobody runs into stays in the library until a change touches it.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
