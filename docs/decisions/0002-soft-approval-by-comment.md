---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Approve a stage with an `/approved` comment, enforced by process

## Context and problem statement

Each stage's document needs approval from a designated role before the next stage starts (PRD §5, C3). The architecture draft proposed GitHub rulesets that require team approvals. How should approval work?

## Considered options

* A. GitHub ruleset that requires approval from named teams per file (hard enforcement)
* B. CODEOWNERS per file (hard; can't require two roles on one file)
* C. A custom approval check in CI (hard)
* D. An `/approved` comment (or a similar action) on the stage PR by someone in the designated role, enforced by managers as a process

## Decision outcome

Chosen option: D. Approval is a deliberate action on the PR, such as an `/approved` comment, by someone in the designated role. It is not enforced by branch protection; managers hold teams to the process.

### Consequences

* Good, because it is simple and needs no rulesets, and approvers only need to be able to comment.
* Good, because an approval is a visible, timestamped record on the PR that tools can read.
* Bad, because nothing technical stops a stage from going ahead without approval.

### Conflict with the PRD (flagged)

PRD C3 (must have) says a stage "refuses to start until its artifact is approved by the designated role", and §9 says to expand only once the pilot team uses the approvals rather than skipping them. Proposed reconciliation, to confirm in design doc 3 (developer workflow): the stage command checks for an `/approved` comment from someone in the designated role and stops if there is none; the developer can override on purpose; every override is recorded and shown on the dashboard. That keeps enforcement soft but makes skipping visible. If accepted, PRD C3's wording should be updated to match.

**Resolved** by [0032](0032-stage-check-stops-and-records-overrides.md): the owner accepted this reconciliation on 2026-10-29.
