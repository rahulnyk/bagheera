---
status: accepted
date: 2026-10-31
deciders: RN (owner)
---
# Intake comments the planning PR's link on the Jira ticket

## Context and problem statement

PRD V5 (should have): Jira reflects stage changes, or at least links to the feature's artifacts. [0006](0006-jira-link-only.md) chose link only and left how the link is made to design doc 5. Each ticket is delivered in one repo, with one planning PR as the story's home ([0030](0030-one-planning-pr-per-feature.md), [0031](0031-one-ticket-one-repo.md)). How does the ticket get its link?

## Considered options

* A. Intake posts one comment on the ticket, with the developer's own Jira access through the plugin's Jira connector
* B. Atlassian's GitHub for Jira app, which lists a ticket's branches, PRs, builds and deployments in its Development panel
* C. Links from GitHub to Jira only

## Decision outcome

Chosen option: A.

* **One comment, once.** When Intake opens the planning PR, it comments on the Jira ticket with a link to the PR and to the story on the dashboard. Later runs don't repeat it.
* **The other way too.** The planning PR's description starts with a link to the ticket.
* **Enough on its own.** The planning PR links its phase and revision PRs, and the dashboard shows the stage, so one link reaches everything.
* **Never a stop.** If the comment can't be posted, Intake says so and carries on; the developer can paste the link by hand.

### Consequences

* Good, because it needs no Jira admin, no new app and no CI access to Jira; the developer's own access is enough.
* Good, because it meets V5's minimum with one more step in Intake.
* Bad, because the ticket shows a link, not the live stage; people in Jira follow it to the dashboard.
* Neutral: if the company already runs GitHub for Jira, it works alongside this unchanged.

## More information

Detail in [design/status-and-dashboard.md, section 7](../design/status-and-dashboard.md#7-the-jira-link). Option B was set aside because on github.com it asks for read and write access to code and PRs, all or nothing, and needs a Jira site admin and a GitHub org owner, which is a lot for a should-have. Option C falls short of V5's minimum.
