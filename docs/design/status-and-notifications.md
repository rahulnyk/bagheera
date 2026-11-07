# Status and notifications

_Design doc 5 of 6 · [All design docs](README.md)_

Anyone can see where a feature stands from its PR, and people hear when something is waiting on them, without a status report or a meeting (PRD P3, C5). This doc says where status comes from, what `/bagheera` reports, how people are notified, and how Jira links to the work.

Covers PRD P3 and C5. It builds on [design doc 3](developer-workflow.md), which set the PR, approvals and overrides that status is read from.

## Principles

* **Derived, never typed.** Status is worked out from the work on GitHub; nobody enters it by hand.
* **One place to look.** For each person, GitHub's review requests list is everything waiting on them (PRD C5).
* **Simple for people.** PMs and tech leads need only GitHub's own pages.

## 1. Where status comes from

GitHub is the record. Nothing else is stored: no status file in product repos, nothing in the library, and no database.

| What | Where it already lives |
| --- | --- |
| A feature | The ticket's PR, on branch `<TICKET>`, in its one repo (design doc 3, section 3) |
| Stage documents and their approvals | The ticket's folder, and `/approved` comments and reviews on the PR, listed with who and when in the PR's description (design doc 3, section 4) |
| Overrides | The override comments the stage commands post (design doc 3, section 4) |
| Phases | The phases Build has ticked off in the PR's description (design doc 3, section 6) |
| Merged, and the library updated | The PR's state, and the library PR that lists it (design doc 1, section 5) |

"How long" comes from GitHub's own timestamps: when a document was pushed, when a reviewer was requested, when the last approval came.

## 2. What `/bagheera` says

`/bagheera` reads the ticket's PR with the developer's own GitHub access and says where the feature stands, what it's waiting on, and the exact next command (PRD P3).

| Stage | When | Waiting on |
| --- | --- | --- |
| **Intake** | The spec is being drafted, has open questions, or waits for approval | The developer, or whoever the questions are for, or the PM |
| **Plan** | The plan is being drafted or waits for approval | The developer, or the tech lead |
| **Build, phase N of M** | Build has ticked off fewer than M phases | The developer |
| **Code review** | The PR is marked ready | Its reviewers |
| **Library update** | The PR has merged, and no library PR lists it yet | The developer |
| **Done** | The library PR that lists it has merged | — |

For example:

```text
PAY-412  Retry failed payments · seeonee/payments-api#512
Stage: Plan, waiting for approval of plan.md for 2 days
Asked: @ravi
Approvals: spec.md by @asha on 2026-11-03
Next: once plan.md is approved, run /bagheera:build
```

## 3. Notifications

_Decided: [ADR 0023](../decisions/0023-github-notifications.md), set by the owner._

GitHub's own notifications.

| When | Who hears | How |
| --- | --- | --- |
| A document is ready for approval | The PM or tech lead named in the spec | They're requested as a reviewer on the PR |
| Questions are posted, or answers are written in | Whoever the questions are for | An @mention in the stage command's comment (design doc 3, section 5) |
| The PR is ready for code review | Its reviewers | GitHub's usual review request |
| A library PR is opened | The ticket's tech lead and PM, or the reviewer the runner names | They're requested as reviewers |

* **Each person chooses how GitHub reaches them,** by email, web or mobile, in their own GitHub settings. The onboarding guide tells PMs and tech leads to switch on email for review requests and mentions.
* **The review requests list is the backstop.** Everything waiting on a person is in it, whether or not they saw the notification.
* **Records aren't notifications.** Overrides, and approvals by someone other than the person asked, stay on the PR, where managers see them.

## 4. The Jira link

* **GitHub to Jira.** The PR's description starts with a link to the ticket, and the branch is named after it.
* **Jira to GitHub.** If the company already runs Atlassian's GitHub for Jira app, it lists the branch and PR in the ticket's Development panel, because the branch name starts with the ticket key. It needs nothing from Bagheera.
