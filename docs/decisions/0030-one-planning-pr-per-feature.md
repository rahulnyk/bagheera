---
status: superseded by [0075](0075-one-pr-per-ticket.md)
date: 2026-10-29
deciders: RN (owner)
---
# One planning PR per feature, with branch rules that never need conflict resolution

## Context and problem statement

Each stage writes a document into the feature's folder and opens or updates a PR (PRD §5). About 100 developers will have a few hundred tickets in flight at once, and a ticket may pass from one developer to another mid-way. Should stages 1–4 each get their own PR, or share one? And how do we keep merges free of conflict resolution and rebases?

## Considered options

* A. One PR per stage, merged once approved: four planning PRs per ticket, each a merge under the repo's own rules
* B. One planning PR per ticket for stages 1–4: Intake opens it, each later stage adds its document, approvals are comments naming the stage (`/approved spec`), and it merges once the test plan is approved

## Decision outcome

Chosen option: B, with four rules:

1. No two open PRs write the same file. The planning PR writes only the feature's folder; each build phase writes its own notes file; nothing shared, such as an index or a status file, is committed to a product repo.
2. Always branch from the main branch, never from an unmerged branch. No stacked PRs.
3. Bring the main branch into a branch by merging (GitHub's "Update branch"), never by rebasing or force-pushing.
4. Branches are named after the ticket, not the person: `PAY-412/planning`, `PAY-412/phase-1`.

Every stage command ends by committing and pushing, so a ticket can be handed over by reassigning it in Jira; the new developer runs `/bagheera PAY-412`, which finds and switches to the ticket's open branch.

### Consequences

* Good, because each ticket has one planning merge instead of four, which matters at a few hundred tickets in flight.
* Good, because the harness's own files never conflict, so updating a branch never needs conflict resolution or a rebase.
* Good, because handover needs no special step: all state is in git and GitHub.
* Bad, because the approval check must tell which document a comment approves and notice edits made after approval (design doc 3, section 4).
* Bad, because product and technical decisions reach the library when the planning PR merges, a few days later than with option A but still before Build; design doc 1, section 5 notes this.
* Neutral: two features changing the same code can still conflict in their build PRs; that is ordinary engineering, which these rules don't change.

## More information

Detail in [design/developer-workflow.md, section 3](../design/developer-workflow.md#3-what-each-stage-writes-and-how-it-reaches-the-main-branch).

Amended by [0040](0040-build-phases-one-at-a-time.md): rule 2 is stricter; a ticket's phases go one at a time, even when independent.
