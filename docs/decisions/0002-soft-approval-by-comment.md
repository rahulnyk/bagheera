---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Anyone approves with an `/approved` comment; the approver is recorded, and who should approve is a process rule

## Context and problem statement

Each stage document needs approval before the next stage starts (PRD §5, C3), and every library change needs a person's approval (K4). One team must be able to adopt Bagheera alone, and lists of who may approve what go out of date as people change. How is approval given and checked, on ticket PRs and library PRs alike?

## Considered options

* A. GitHub reviews required by branch rules, with `CODEOWNERS` per path
* B. An `/approved` comment that counts only from the PM or tech lead named in the spec
* C. An `/approved` comment from anyone, with the approver's name recorded; who should approve is a process rule

## Decision outcome

Chosen by the owner: C.

* **One way to approve, on every PR.** A comment with `/approved <stage>` on its own line: `/approved spec` and `/approved plan` on the ticket's PR, and `/approved library` on a library PR. A GitHub review whose text contains the same line counts too.
* **Anyone can approve.** The stage check doesn't look at who commented, the document's author included.
* **The approver is recorded.** The stage check lists each approval it counts, with who and when, in the PR's description, and the library update copies the names into each page's `approved_by`.
* **Who should approve is a process rule:** the PM approves the spec, the tech lead approves the plan, and the ticket's tech lead or PM approves its library PR. Every approval names who gave it, so managers can see when someone else did.
* **Named so they can be asked.** The spec's header names the ticket's PM and tech lead; Intake suggests them and the developer confirms. Each command requests the person whose approval it needs as a reviewer, so GitHub notifies them.
* **No `CODEOWNERS`** and no lists of approvers anywhere.

### Consequences

* Good, because a team adopts it with no lists of people and nothing from org admins.
* Good, because approvals look the same on every PR, and each one names who gave it.
* Good, because an absent PM or tech lead never blocks a ticket: someone else can approve, on the record.
* Bad, because nothing technical stops the wrong person approving, the author included. It shows on the PR, and managers act on it.

## More information

The stage check and overrides: [0032](0032-stage-check-stops-and-records-overrides.md). Detail in [design/developer-workflow.md](../design/developer-workflow.md).
