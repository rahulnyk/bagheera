---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Build the dashboard on an org-level GitHub Project

## Context and problem statement

The dashboard lists stories with filters and "waiting on me" (PRD V1), shows stage, status, who and how long, phases and a link to the PR needing action (V2), and must be usable without developer tools (V3). Status is derived from GitHub and written only into the dashboard ([0049](0049-github-is-the-record-dashboard-holds-derived-status.md)). What is the dashboard built on?

## Considered options

* A. An org-level GitHub Project: planning PRs as story rows and phase and revision PRs as linked rows, with fields filled by the sync job and saved views per audience
* B. A page the sync job generates, for example on GitHub Pages
* C. Jira dashboards, with the stage written into Jira fields

## Decision outcome

Chosen option: A. One org-level project. Each story is a row keyed on its planning PR; phase and revision PRs are added as rows linked to their story, so "waiting on me" lists the exact PRs that need the viewer. The sync job is the only writer of the project's fields. Shipped stories are archived automatically.

### Consequences

* Good, because it is configuration, not a build, and sits where approvals already happen.
* Good, because saved views map onto V1's filters, and GitHub's built-in charts cover simple trends.
* Good, because a project holds up to 50,000 items and 50 fields, which is years of stories.
* Bad, because project fields can't hold cost data; adoption and cost (V6) stay in Claude's analytics and meet the stories only in the weekly summary.
* Bad, because viewers need read access to the repos to see the PR rows, which PMs, QA and leadership have ([0001](0001-github-access-for-pms-qa-and-leadership.md)).

## More information

Detail in [design/status-and-dashboard.md, section 3](../design/status-and-dashboard.md#3-what-the-dashboard-is-built-on). Option C was set aside because it reverses [0006](0006-jira-link-only.md).
