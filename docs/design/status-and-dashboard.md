# Status and dashboard

_Status: draft complete, awaiting the owner's approval · Design doc 5 of 6 · [All design docs](README.md)_

One simple dashboard shows every story, what it's waiting on and for how long, so PMs, tech leads, QA, managers and leadership can see where things stand without asking anyone (PRD G4, G5). This doc says where status comes from, how it's worked out, what the dashboard is built on, its views, notifications, the weekly summary and the Jira link.

Covers PRD V1–V7 and C5; adoption and cost reporting (V6) is shared with [design doc 6](README.md). It builds on [design doc 3](developer-workflow.md), which set the PRs, approvals and overrides that status is read from, and on [design doc 4](standards-and-review.md), which set the findings and dismissals reported weekly.

## Principles

* **Derived, never typed.** Status is worked out from the work on GitHub; nobody enters it by hand (PRD V4).
* **View-only.** People act in GitHub, where approvals already happen; the dashboard links them there ([0005](../decisions/0005-view-only-dashboard.md)).
* **One place to look.** For each person, one list of what's waiting on them (PRD C5, V1).
* **Simple for people.** Usable without developer or ops tools (PRD V3; design doc 3).

## 1. Where status data lives

_Decided: [ADR 0049](../decisions/0049-github-is-the-record-dashboard-holds-derived-status.md), following [0005](../decisions/0005-view-only-dashboard.md)._

GitHub is the record. Every 15 minutes the dashboard sync job reads each story's PRs, comments, checks and files, works out its status (section 2), and writes the result into the dashboard (section 3). Nothing else is stored.

### What it reads

| What | Where it already lives |
| --- | --- |
| A story | Its planning PR, `<TICKET>/planning`, in its one repo (design doc 3, section 3) |
| Stage documents and their approvals | The feature folder, and `/approved` comments and reviews on the planning PR (design doc 3, section 4) |
| Overrides and warnings | The comments the stage commands post; missing QA approvals and tests-first commits (design doc 3, section 4) |
| Phases | The phase PRs, `<TICKET>/phase-N`, and whether they've merged |
| Revisions | Revision PRs, `<TICKET>/revision-N` (design doc 3, section 8) |
| Criteria results, findings and dismissals | Verify's check runs and the results comment on each phase PR (design doc 4, section 4) |
| Demo approval | `/approved demo` on the planning PR (design doc 3, section 7) |
| Shipped | The feature entry in the library, written at Close (design doc 1, section 5) |
| The Jira ticket | The ticket key in the spec's header and the branch names |

### Rules

* **One row per story,** keyed on its planning PR, which stays the story's home on GitHub even after it merges.
* **"How long" comes from GitHub's own timestamps:** when the PR opened, when the last approval or push happened.
* **History and throughput are computed when needed,** for the weekly summary (section 6), from merged PRs and the shipped dates in the library's feature entries.
* **Only the sync job writes the dashboard.** If a value is wrong, the fix is in the rule or in the work, and the next sync overwrites it.
* **Nothing is stored anywhere else:** no status file in product repos (design doc 3, section 3, rule 1), nothing in the library, which holds settled knowledge only (design doc 1, section 5), and no database.
* **It stays within GitHub's limits.** The sync reads through the harness's GitHub App, batching its queries.

## 2. The status model

_Decided: [ADR 0050](../decisions/0050-named-people-per-story-team-fallback.md)._

Each story has a stage, a status, who it's waiting on, and since when, plus a few markers for managers.

### Stage

The PRD's eight stages (design doc 3), worked out from the work:

| Stage | When |
| --- | --- |
| 1 Intake to 4 Test plan | The planning PR is open; the stage is the latest document in it |
| 5 Build, phase N of M | A phase PR is open as a draft, or the next phase hasn't started |
| 6 Verify, phase N of M | The phase PR is marked ready for review: Verify and people are reviewing it |
| 7 Demo | Every phase has merged, and the demo isn't accepted yet |
| 8 Close | The PM has approved the demo; the feature entry is on its way into the library |
| Revision N | A revision PR is open (design doc 3, section 8); afterwards the story returns to Build |

### Status

| Status | Means | Waiting on |
| --- | --- | --- |
| **Drafting** | The developer is writing a document or code: the next document isn't ready, or the PR is a draft | The developer |
| **Questions open** | Questions are posted on the PR and not yet written into the document (design doc 3, section 5) | Whoever they're for |
| **Waiting for approval** | A document is ready and lacks an approval from a required role | The named approvers |
| **In review** | A phase PR is ready, Verify passes, and code review is pending | The PR's reviewers |
| **Fixing** | Verify fails, or a reviewer asked for changes | The developer |
| **Waiting for demo** | Every phase has merged, and there's no `/approved demo` yet | The developer, to give the demo; then the PM |
| **Shipped** | The feature entry has merged into the library | — |

"Since" is when the story entered its current status, from GitHub's timestamps (section 1).

### Who a story waits on

* **Named at Intake.** The spec's header records `pm`, `tech_lead` and `qa` as GitHub usernames. Intake suggests the PM from the Jira ticket and the others from the domain's teams in `domains.yaml`, and the developer confirms. Changing the names later doesn't cancel an approval of the spec, because it doesn't change what the spec says.
* **Requested as reviewers.** When a document is ready, the stage command requests the named approvers as reviewers on the planning PR: the PM for the spec, the tech lead for the architecture, both for the plan, and the PM and QA for the test plan. GitHub notifies them and lists the PR in their review requests (section 5). Phase PRs use the repo's usual code reviewers.
* **Team fallback.** Anyone in the role's team can still approve (design doc 3, section 4), and the developer can change a name, so an absence never blocks a story.
* **Shown on the dashboard.** The sync makes whoever the story is waiting on the planning PR's assignees, so "waiting on me" is a simple filter (section 4).

### Markers

Markers flag what managers act on (design doc 3, principles). They show next to the status and in the weekly summary (section 6).

| Marker | When |
| --- | --- |
| **Override** | A stage started, or the planning PR merged, without a required approval (design doc 3, section 4) |
| **No QA review** | The test cases have no QA approval ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)) |
| **No tests-first** | A phase PR has no tests-first commit ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)) |
| **Self-dismissal** | A Verify finding was dismissed by the PR's own author ([0048](../decisions/0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md)) |
| **Stuck** | The same status for more than 2 working days; a starting point to tune in the pilot |

## 3. What the dashboard is built on

_Decided: [ADR 0051](../decisions/0051-dashboard-on-an-org-github-project.md), following [0005](../decisions/0005-view-only-dashboard.md)._

The dashboard is one org-level GitHub Project, titled **Bagheera dashboard** ([0056](../decisions/0056-name-the-dashboard-plainly.md)). It is configuration, not a build: the sync job fills it in, and people read it.

### Rows

* **One row per story:** its planning PR, added by the sync job when Intake opens it. The row's title opens the PR.
* **Phase and revision PRs are rows too,** linked to their story by the ticket. Most views show story rows only; "waiting on me" also shows the exact phase or revision PR that needs the viewer, which gives V2's "direct link to the PR needing approval".
* **Shipped stories are archived automatically** after a few weeks, using the project's own auto-archive, so the live views stay short. A project holds up to 50,000 items, archive included.

### Fields

The sync job writes these and nothing else does (section 1).

| Field | Kind | Example |
| --- | --- | --- |
| Ticket | Text | `PAY-412` |
| Row | Single select | Story · Phase · Revision |
| Stage | Single select | 4 Test plan |
| Status | Single select | Waiting for approval |
| Since | Date | 2026-11-03 |
| Waiting on | GitHub's own Assignees | `@asha` |
| Team | Single select | payments, from `domains.yaml` |
| PM | Text | `@asha` |
| Phases | Text | 1 / 3 |
| Criteria | Text | 5 / 7 passing |
| Markers | Text | Override · No QA review |

GitHub's own fields add the repository and the reviewers. That's 11 fields of the 50 a project allows. "For how long" shows as the "Since" date, and the "Stuck" marker flags anything over the limit (section 2).

### Who can see and change it

* **Visible to everyone in the org** who can read the repos, which includes PMs, QA and leadership ([0001](../decisions/0001-github-access-for-pms-qa-and-leadership.md)).
* **Written only by the sync job,** through the harness's GitHub App. The platform team administers the project; everyone else can read it and save their own views (section 4).
* **No automations besides auto-archive,** so nothing changes a field behind the sync job's back.

### What it doesn't hold

Cost and adoption by team (PRD V6) stay in Claude's own analytics (design doc 6); project fields can't hold them. They meet the stories only in the weekly summary (section 6).

## 4. Views and filters

_Decided: [ADR 0052](../decisions/0052-five-shared-dashboard-views.md)._

Five shared views, set up once by the platform team, appear as tabs. Anyone can filter further and save their own.

| View | Layout | Shows | Order |
| --- | --- | --- | --- |
| **Waiting on me** (first tab) | Table | Every story, phase or revision row assigned to the viewer, which is everything waiting on them (section 2) | Oldest first |
| **All stories** | Table | Story rows that haven't shipped, filterable by team, PM, stage and status | Grouped by team |
| **By stage** | Board | Story rows in one column per stage | — |
| **Needs attention** | Table | Story rows that are stuck or carry a marker: override, no QA review, no tests-first, self-dismissal | Oldest first |
| **Shipped** | Table | Shipped stories, until they're archived | Newest first |

For example, a PM opening the dashboard sees:

```text
Waiting on me                                      Since    Status
PAY-412  Retry failed payments · planning PR       2 days   Waiting for approval: test plan
PAY-398  Refund webhooks · phase 2 PR              5 hours  Questions open: Q7
PAY-350  Saved cards · planning PR                 4 days   Waiting for demo  ⚑ Stuck
```

* **Filtering** uses the project's filter bar on the sync job's fields, for example `team:payments status:"Waiting for approval"`.
* **"Where is feature X, and what is it waiting on?"** Search for the ticket in All stories: the row shows its stage, status, who it's waiting on and since when (PRD §7's leadership measure).
* **Each row opens its PR,** where people act; the dashboard itself is view-only ([0005](../decisions/0005-view-only-dashboard.md)).

## 5. Notifications

_Decided: [ADR 0053](../decisions/0053-github-notifications-now-slack-later.md), set by the owner._

GitHub's own notifications, for now. Slack comes later.

| When | Who hears | How |
| --- | --- | --- |
| A document is ready for approval | The named approvers | They're requested as reviewers on the planning PR (section 2) |
| Questions are posted, or answers are written in | Whoever the questions are for | An @mention in the stage command's comment (design doc 3, section 5) |
| A phase PR is ready for code review | Its reviewers | GitHub's usual review request |
| Verify finds a problem | The PR's author | GitHub's notice of a failed check |
| Anything starts waiting on someone else | The new person | The sync makes them an assignee, and GitHub notifies people when they're assigned |

### Rules

* **Each person chooses how GitHub reaches them,** by email, web or mobile, in their own GitHub settings. The onboarding guide tells PMs and approvers to switch on email for review requests, mentions and assignments.
* **"Waiting on me" is the backstop.** Everything waiting on a person is always in that view, whether or not they saw the notification (section 4).
* **Records aren't notifications.** Overrides, dismissals and other markers go to the Needs attention view and the weekly summary (sections 4 and 6), not to anyone's inbox.

### Later: Slack

GitHub's scheduled reminders can post a daily list to each team's Slack channel of the PRs its members have been asked to review. It's configuration only: an org owner authorises the Slack workspace once, and team maintainers set the times. It's planned for after the pilot in `docs/plan.md`.

## 6. The weekly summary

_Decided: [ADR 0054](../decisions/0054-weekly-summary-as-a-github-issue.md)._

Once a week the dashboard sync writes a one-page summary as a GitHub issue in `seeonee/bagheera` (PRD V7). It takes in design doc 1's library summary as one section, so there's one weekly summary, not two.

### How it reaches people

* **A new issue every Monday morning,** covering the week before, titled for example `Bagheera weekly summary: 26 Oct – 1 Nov 2026` and labelled `weekly-summary`. The sync job pins it to the top of the repo's issues and closes last week's.
* **Readers join one team.** The issue @mentions `@seeonee/bagheera-weekly`, which managers, leadership and the platform team join; anyone can ask to join. GitHub tells them by email, web or mobile, as they've chosen (section 5).
* **Owners hear about their own part.** A standard's owners are @mentioned on the rows about their rules (PRD R6; design doc 4, section 4). A domain's owners are @mentioned on library PRs of theirs that have waited more than five working days (design doc 1, section 8). Only owners with something listed are mentioned that week.
* **Linked from the dashboard.** The project's README links to the list of past summaries, so they're one click away and searchable.
* **Comments are open,** so a reader can ask about a line and everyone sees the answer.

### What it says

Six short sections. Each gives counts and the five oldest or largest items, with links to their PRs or pages; the rest is folded under "show all", so the summary stays one page.

| Section | What it shows | Mainly for | Worked out from |
| --- | --- | --- | --- |
| **1. Shipped** | Stories shipped, by team, against the average of the last four weeks; median working days from Intake to shipped | Leadership | Feature entries in the library; planning PRs |
| **2. Stuck** | Stories in the same status for more than 2 working days, grouped by what they wait on (spec, architecture, plan, test plan, code review, demo), with who and since when; the median time from a document being ready to the PM's first response (PRD §7, G4) | Leadership, managers | The dashboard's fields and the planning PRs |
| **3. For managers** | Overrides, no QA review, no tests-first and self-dismissals, by team, each with its PR | Managers | The markers (section 2) |
| **4. Standards findings** | Findings and dismissals by team and by rule, rules with the most findings first, with who dismissed each and why | Standards owners, managers | Verify's check runs (design doc 4, section 4) |
| **5. Library health** | Open library PRs by domain and age; service pages overdue for verification, and the share verified in the last 90 days; findings opened and closed; planted-instruction alarms, and how many were false | Page owners, the platform team | The library repo (design doc 1, section 8) |
| **6. Adoption and cost** | Active users, sessions, spend and PRs with AI help, by team (PRD V6); CI cost per merged PR (PRD §7) | Leadership, the platform team | Claude's analytics and CI usage (design doc 6) |

For example:

```text
Bagheera weekly summary: 26 Oct – 1 Nov 2026               @seeonee/bagheera-weekly

1. Shipped      4 stories (4-week average 3) · median 9 working days from Intake
                payments 2 · checkout 1 · accounts 1
2. Stuck        3 stories
                Test plan  PAY-412  Retry failed payments   4 days  @asha, @ravi
                Demo       PAY-350  Saved cards             6 days  @meera
                PMs' median first response: 0.6 working days
3. Managers     2 overrides · 1 no QA review · 3 no tests-first · 1 self-dismissal
4. Standards    14 findings · 5 dismissed
                sec-3  6 findings, 2 dismissed  @seeonee/architecture-council
5. Library      7 open PRs, 1 over five working days (@seeonee/payments-tech-leads)
                86% of service pages verified in the last 90 days
6. Cost         41 active users · 312 sessions · spend by team · CI cost per merged PR
```

### Rules

* **It reports; it doesn't act.** Every fix happens on the PR or page a line links to, like the dashboard ([0005](../decisions/0005-view-only-dashboard.md)).
* **Derived, never typed.** Everything comes from GitHub (section 1), plus Claude's analytics for cost. History and trends are worked out fresh each week, so nothing extra is stored; the closed issues are the archive.
* **A missing source is said, not hidden.** If a section's source isn't connected yet, such as cost before design doc 6 is in place, the section says so.
* **Same App, one more permission.** The sync job writes the issue through the harness's GitHub App, which needs to write issues in `seeonee/bagheera` (design doc 6).

## 7. The Jira link

_Decided: [ADR 0055](../decisions/0055-intake-comments-the-link-on-the-jira-ticket.md), following [0006](../decisions/0006-jira-link-only.md)._

Jira links to the work; it doesn't show the stage (PRD V5's minimum). The stage is on the dashboard, one click away.

### How the link is made

* **Jira to GitHub.** When Intake opens the planning PR, it posts one comment on the Jira ticket, with the developer's own Jira access through the plugin's Jira connector (the same connector that reads the ticket):

  ```text
  Bagheera: planning PR seeonee/payments-api#512 · status on the Bagheera dashboard
  ```

  The dashboard link opens the All stories view filtered to the ticket.
* **GitHub to Jira.** The planning PR's description starts with a link to the ticket, and the dashboard's Ticket field shows the key (section 3).
* **One link is enough.** Each ticket has one repo and one planning PR (design doc 3, section 3), which links its phase and revision PRs. A major change becomes a new ticket, which gets its own link at its own Intake.

### Rules

* **Once per ticket.** Later runs of Intake, including the finalising run, don't comment again.
* **Never a stop.** If the comment can't be posted, for example because the developer can't comment on that ticket, Intake says so and carries on, and the developer can paste the link by hand.
* **Nothing else writes to Jira.** CI has no Jira access, and nobody keeps Jira fields in step.
* **If the company already runs Atlassian's GitHub for Jira app,** it also lists the branches and PRs in the ticket's Development panel, because branch names start with the ticket key. It needs nothing from Bagheera.

