---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# The stage check stops without a current approval; a developer can override it, on the record

## Context and problem statement

PRD C3: a stage refuses to start until its document is approved, unless the developer overrides on purpose; every override is recorded on the PR, and editing a file locally never counts as approval. Approvals are `/approved` comments on the ticket's PR ([0002](0002-soft-approval-by-comment.md)). What does a stage command do when an earlier document isn't approved?

## Considered options

* A. Stop and say what's missing; the developer can override on purpose, with a reason, posted on the PR
* B. Stop, with no way past but an approval
* C. Warn and carry on

## Decision outcome

Chosen by the owner: A.

* **Checked first.** Every stage command runs the stage check before anything else, reading the PR's comments, reviews and commits with the developer's own GitHub access. Plan needs an approved spec; Build needs an approved spec and plan.
* **Only a current approval counts.** An approval counts only if it's newer than the last commit that changed the document: any change, a typo fix included, cancels it, and someone approves again.
* **A stop says why.** Without what it needs, the command stops, says what's missing and who was asked, and links the PR.
* **Overrides.** `--override "<reason>"` goes ahead anyway; the reason can't be empty. The command posts the override on the PR: the stage, what was missing, the reason and who.
* **An override isn't an approval.** Nothing in a document that was never approved reaches the library as approved work.
* **Local edits never count.** Approvals exist only as GitHub comments and reviews.

### Consequences

* Good, because C3's "refuses to start" holds by default, and a local edit never counts.
* Good, because enforcement stays soft, while every skip is on record where the work is.
* Good, because the pilot can count how often approvals were skipped, and why (PRD §9).
* Bad, because a determined team can override every stage; managers act on what the PR shows.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
