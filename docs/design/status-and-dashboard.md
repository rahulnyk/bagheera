# Status and dashboard

_Status: in progress · Design doc 5 of 6 · [All design docs](README.md)_

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

The dashboard is one org-level GitHub Project. It is configuration, not a build: the sync job fills it in, and people read it.

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

## Still to come

4. Views and filters
5. Notifications
6. The weekly summary
7. The Jira link
