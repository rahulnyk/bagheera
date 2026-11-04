---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Name the people each story waits on, with the team as fallback

## Context and problem statement

Each story on the dashboard shows a stage, a status, who it is waiting on and since when (PRD V2). Approvals count by role, so any PM in the domain can approve ([0032](0032-stage-check-stops-and-records-overrides.md)). But "waiting on me" (PRD C5) only works if a story names a person. Who does "waiting on" name?

## Considered options

* A. Named people per story: Intake records the story's PM, tech lead and QA; stage commands request them as reviewers when a document is ready; anyone in the role's team can still approve
* B. Teams only: a story waits on the role's team
* C. GitHub's automatic team review assignment, by round-robin or load

## Decision outcome

Chosen option: A.

* **Named at Intake.** The spec's header records `pm`, `tech_lead` and `qa`. Intake suggests the PM from the Jira ticket and the others from the domain's teams; the developer confirms. Changing these names later doesn't cancel an approval of the spec.
* **Requested as reviewers.** When a document is ready, the stage command requests the named approvers as reviewers on the planning PR. GitHub notifies them and lists the PR in their review requests.
* **Team fallback.** Anyone in the role's team can still approve, and the developer can change a name, so an absence never blocks a story.
* **Status model.** Seven statuses (drafting, questions open, waiting for approval, in review, fixing, waiting for demo, shipped) and a few markers for managers (override, no QA review, no tests-first, self-dismissal, stuck), as set out in design doc 5, section 2.

### Consequences

* Good, because "waiting on me" means a person, and GitHub's own notifications and review list cover much of C5 with nothing to build.
* Good, because a holiday or handover doesn't block a story.
* Bad, because the names must be kept current; the developer updates them when people change.

## More information

Detail in [design/status-and-dashboard.md, section 2](../design/status-and-dashboard.md#2-the-status-model). Adds three fields to the spec's header ([design doc 3, section 3](../design/developer-workflow.md#3-what-each-stage-writes-and-how-it-reaches-the-main-branch)).

Amended by [0072](0072-named-pm-and-tech-lead-approve.md): in the first system, only the PM and tech lead named in the spec approve, with no team fallback and no QA name.
