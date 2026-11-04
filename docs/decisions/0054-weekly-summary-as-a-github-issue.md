---
status: accepted
date: 2026-10-31
deciders: RN (owner)
---
# Post the weekly summary as a GitHub issue

## Context and problem statement

PRD V7 asks for "a weekly one-page summary: what shipped, what's stuck at which approval, cost by team". Earlier decisions also send it the managers' markers ([0038](0038-managers-enforce-qa-review-and-tests-first.md)), standards findings and dismissals by team and rule for their owners (PRD R6, [0048](0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md)), and library health ([0020](0020-send-findings-to-owners-as-library-prs.md)). Notifications are GitHub's own for now ([0053](0053-github-notifications-now-slack-later.md)). Where does the summary go each week?

## Considered options

* A. A weekly GitHub issue in `seeonee/bagheera`, opened by the sync job, mentioning a readers team and the owners named in it
* B. A page on the dashboard only: the sync job rewrites the GitHub Project's README each week
* C. An email to a mailing list, sent by the sync job

## Decision outcome

Chosen option: A.

* **One issue a week.** Every Monday morning the dashboard sync opens an issue covering the week before, labelled `weekly-summary` and pinned; last week's is closed.
* **Who hears.** The issue @mentions `@seeonee/bagheera-weekly`, a team that managers, leadership and the platform team join. A standard's owners are mentioned on the rows about their rules, and a domain's owners on its library PRs that have waited more than five working days.
* **One summary, not two.** The library summary from design doc 1 becomes one of its sections.
* **Six sections:** shipped, stuck by approval, markers for managers, standards findings, library health, and adoption and cost. Each gives counts and the top items with links; the rest is folded away so it stays one page.

### Consequences

* Good, because it reaches people through GitHub notifications, with nothing new to run.
* Good, because past weeks stay searchable, and readers can ask about a line in a comment.
* Bad, because GitHub org owners create one more team, and readers must join it.
* Bad, because the cost section waits on how Claude's analytics are reached (design doc 6); until then it says it isn't connected.

## More information

Detail in [design/status-and-dashboard.md, section 6](../design/status-and-dashboard.md#6-the-weekly-summary). Option C was set aside because it needs email credentials and a list to keep, against [0053](0053-github-notifications-now-slack-later.md).

Amended by [0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md): in the pilot, the summary is posted by `/bagheera:update-dashboard` when a manager runs it and one is due, with the manager's own access.
