---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The newest page wins, contradictions are flagged when used, and each page records its approvers

## Context and problem statement

[0070](0070-knowledge-expires-and-every-change-is-checked-once.md) dropped the weekly check, which looked for contradictions between pages, and the sources on every statement, which recorded who approved each piece of knowledge. Pages still can disagree, and people still need to know who approved what a page says.

## Decision outcome

Set by the owner.

* **The newest wins.** When two pages disagree, the one with the later `updated` date is taken as more likely right.
* **Flagged when used.** A session or command that relies on pages that disagree says so, names both, and goes with the newer. `/bagheera:update-library` also notes in its PR any page its changes contradict, so the owners can fix it.
* **Expired pages lose.** A contradicting page past its `expires` date is ignored, without a flag.
* **Approvers recorded.** Every page's header records who approved what it says: the people whose `/approved` comments approved the work it came from, such as the PM on a spec or the tech lead on a plan, and the owner who approved the library PR. Each change replaces the list with the approvals behind it.
* **Later, not now:** a small workflow for managers to review expired knowledge and renew it.

### Consequences

* Good, because nothing runs on a timer, and contradictions surface exactly where they matter.
* Good, because every page says who stands behind it.
* Bad, because a contradiction nobody runs into stays in the library until a change touches it.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md#changes-since-approval).
