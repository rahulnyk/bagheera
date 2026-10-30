---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# The stage check stops without approval, and deliberate overrides are recorded

## Context and problem statement

PRD C3 (must have) says a stage "refuses to start until its artifact is approved by the designated role; editing a file locally must not count as approval". The owner chose soft enforcement: an `/approved` comment, with managers holding teams to the process ([0002](0002-soft-approval-by-comment.md)). With one planning PR per feature ([0030](0030-one-planning-pr-per-feature.md)), an approval is a comment naming the document, such as `/approved spec`. What should a stage command do when an earlier document isn't approved?

## Considered options

* A. The command stops and says who it's waiting on; the developer can override on purpose, with a reason; every override is posted on the PR and shown on the dashboard
* B. Hard enforcement: also a required CI check, so the planning PR can't merge until every document is approved; no overrides except by repo admins
* C. Warn only: the command warns and carries on; the dashboard shows missing approvals

## Decision outcome

Chosen option: A. Each stage command runs the stage check first. Without a current approval from the designated role for every earlier document, it stops, names what's missing and who it's waiting on, and links to the PR. `--override "<reason>"` lets the developer go ahead; the command posts the override as a comment on the PR, and the dashboard and weekly summary show it. An override is not an approval: the document stays unapproved, nothing in it reaches the library as approved work, and approving it later clears the flag. Merging the planning PR without every approval is not blocked, and counts as an override.

### Consequences

* Good, because C3's "refuses to start" holds by default, and a local edit never counts: approvals are GitHub comments from the right people.
* Good, because enforcement stays soft, as the owner chose, while every skip is visible to managers.
* Good, because the pilot can measure whether approvals are used or skipped (PRD §9).
* Bad, because a determined team can still override every stage; managers act on what the dashboard shows.
* PRD C3's wording should change to add "unless the developer overrides on purpose; every override is recorded and shown".

## More information

Detail in [design/developer-workflow.md, section 4](../design/developer-workflow.md#4-approval). Resolves the conflict flagged in [0002](0002-soft-approval-by-comment.md).

Amended by [0038](0038-managers-enforce-qa-review-and-tests-first.md) (QA's approval of test cases warns instead of stopping) and [0039](0039-wording-fixes-keep-an-approval.md) (marked wording fixes keep an approval).
