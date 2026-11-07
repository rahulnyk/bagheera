---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Notify people through GitHub's own notifications

## Context and problem statement

PRD C5: PMs are notified when something needs their review, and see everything waiting on them in one list. The stage commands request the PM and tech lead as reviewers on the ticket's PR and @mention them in their question comments. Should anything be added to GitHub's own notifications?

## Considered options

* A. GitHub's own notifications only
* B. A, plus GitHub's scheduled reminders in Slack: a daily list per team of PRs awaiting its review
* C. A, plus a daily digest per person, built and sent by Bagheera

## Decision outcome

Set by the owner: A.

* People hear through GitHub, by email, web or mobile, as each person chooses in their GitHub settings: review requests and @mentions.
* GitHub's review requests list is the one list of everything waiting on a person.
* The onboarding guide tells PMs and tech leads to switch on email for review requests and mentions.

### Consequences

* Good, because there is nothing to set up or run.
* Bad, because it depends on each person's GitHub notification settings and attention; the review requests list is the backstop.

## More information

Option B needs an org owner to connect Slack, so it is part of the [org-adoption proposal](../proposals/org-adoption.md).
