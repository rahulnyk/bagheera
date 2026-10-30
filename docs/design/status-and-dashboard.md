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

## Still to come

2. The status model: stages, states, who it's waiting on
3. What the dashboard is built on
4. Views and filters
5. Notifications
6. The weekly summary
7. The Jira link
