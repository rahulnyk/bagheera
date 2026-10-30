---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# GitHub's own notifications for now; Slack later

## Context and problem statement

PRD C5: PMs are notified when something needs their review, and see everything waiting on them in one list. The list is the dashboard's "Waiting on me" view ([0052](0052-five-shared-dashboard-views.md)). Stage commands already request named reviewers and @mention people for questions ([0050](0050-named-people-per-story-team-fallback.md)). Should anything be added to GitHub's own notifications?

## Considered options

* A. GitHub's own notifications only
* B. A, plus GitHub's scheduled reminders in Slack: a daily list per team of PRs awaiting its review
* C. A, plus a daily digest per person built and sent by the sync job

## Decision outcome

Set by the owner: A for now, with Slack integrated later. People are notified through GitHub, by email, web or mobile as each person chooses in their GitHub settings: by review requests, by @mentions, and by assignment, since the sync job makes whoever a story is waiting on the PR's assignee. The onboarding guide tells PMs and approvers to switch on email for review requests, mentions and assignments. Slack integration is planned for later in `docs/plan.md`; GitHub's scheduled reminders (option B) are the likely route.

### Consequences

* Good, because there is nothing to set up or run.
* Good, because assignment notifications cover every status that waits on a person, not only approvals.
* Bad, because it depends on each person's GitHub notification settings and attention; "Waiting on me" is the backstop.

## More information

Detail in [design/status-and-dashboard.md, section 5](../design/status-and-dashboard.md#5-notifications).
