---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Blocking findings fail Verify; a tech lead can dismiss one with a reason

## Context and problem statement

Verify reviews every PR as a required check ([0037](0037-verify-as-an-org-ruleset-workflow.md)), so whatever fails it blocks the merge. The reviewer is a separate Claude (PRD R3), so it will sometimes be wrong. Standards are blocking or advisory per page ([0041](0041-blocking-per-page-numbered-rules.md)). What happens when the review reports a blocking rule as broken?

## Considered options

* A. The check fails; a tech lead of the repo's domain, not the PR's author, can dismiss a finding with a reason; Verify then re-runs without it; every dismissal is recorded and reported by rule
* B. It never blocks: blocking findings are flagged and recorded, and managers enforce them
* C. A hard block with no dismissal: fix the code, change the rule, or have an admin bypass the ruleset

## Decision outcome

Chosen option: A.

* **Dismissing.** A member of the domain's `owners` team, never the PR's author, replies `/dismiss <rule ID> <reason>` on the finding. Verify re-runs without it on the next push, or when the 15-minute dashboard sync re-runs it, since GitHub doesn't re-run required workflows on a comment.
* **Only written blocking rules can fail the check.** Security issues the reviewer finds that no written rule covers are advisory comments, labelled as such; owners who want one to block write a rule.
* **Findings are shown on the PR's lines,** each citing the rule ID, with one summary comment; re-runs update those comments rather than adding new ones.
* **The record lives in the Verify check run on each PR.** The weekly summary reads it to report findings and dismissals by team and rule (PRD R6).

### Consequences

* Good, because owners decide what blocks, and a reviewer mistake costs minutes, not an admin request.
* Good, because dismissals tell owners which rules are unclear or wrong.
* Good, because it mirrors the stage check: stop by default, a person can override, and it's on record.
* Bad, because a re-run after a dismissal can take up to 15 minutes unless someone pushes or re-runs it by hand.
* Bad, because the dashboard sync needs permission to re-run workflows in product repos (design doc 6).

## More information

Detail in [design/standards-and-review.md, section 4](../design/standards-and-review.md#4-the-pr-review).

Amended by [0048](0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md): whoever reviews the PR may dismiss, with no rule on who; who and why are always recorded, and self-dismissals are highlighted.
