---
status: proposed
date: 2026-11-14
deciders: RN (owner)
---
# One approval: the PM approves the spec, in the portal

## Context and problem statement

Approval from the PM, then the tech lead, then sometimes the PM again can't be kept up in practice. Which approvals remain?

## Considered options

* A. The PM approves the spec. The plan has no gate; the tech lead reviews the plan, code and wiki in code review
* B. The PM approves the spec, and the tech lead approves the plan
* C. No approvals; code review only

## Decision outcome

Proposed: A.

* The approver named in the spec, normally its PM, approves it in the portal with one click, once no question for them is open.
* The portal commits the approval as its GitHub App. It counts only while it's the last commit to `spec.md`, so any later change clears it, hand edits included.
* When approving again, the PM sees only what changed since their last approval.
* Plan and Build stop until the spec is approved. There's no override: a ticket with no PM names the developer or the tech lead as approver.
* Code review, as today, is where the tech lead reviews the plan, the code and the wiki changes.

### Consequences

* Good, because there's one gate, owned by whoever owns the requirements.
* Good, because checking the spec against the code ([0004](0004-the-spec-is-checked-against-the-code-while-drafted.md)) removes most reasons to approve again.
* Bad, because a tech lead's objection to the plan can come late, at code review. If that keeps happening in the pilot, move to B.
