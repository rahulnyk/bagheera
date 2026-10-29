---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# One ticket, one repo

## Context and problem statement

A feature's folder (`specs/<TICKET>/`) has to live in exactly one repo, but a piece of work can touch several, for example a frontend and a backend. Where does the folder live, and how do the other repos' changes relate to it?

## Considered options

* A. The folder lives in the repo where Intake runs; build PRs in other repos link back to it
* B. One ticket per repo: work that spans repos is split into separate tickets, one per repo, before Intake

## Decision outcome

Chosen option: B, set by the owner to keep the harness simple. Each Jira ticket is delivered in exactly one repo, and its folder lives there. A frontend and a backend change are two tickets, linked in Jira. Intake records the linked tickets in the spec, so the documents and the library's feature entries point to each other. An interface the two share is decided once, in one ticket's architecture, and reaches the other through the library's decision record.

The harness holds to the rule in three places: Intake stops if the ticket already has a planning PR in another repo; a CI check fails a ticket branch in a repo that doesn't hold that ticket's folder; and Intake raises an open question when a ticket looks as if it needs changes in another repo.

### Consequences

* Good, because every ticket has one folder, one set of branches and one repo, and nothing has to coordinate across repos.
* Good, because it matches PRD §5, which writes each stage's document "into the feature's repo".
* Bad, because a PM reviews one spec per repo for what may feel like one feature; each spec is smaller.
* Bad, because tickets that depend on each other coordinate through Jira links and the library rather than one shared plan.
* Neutral: in a repo holding several services, one ticket may still touch several of them.

## More information

Detail in [design/developer-workflow.md, section 3](../design/developer-workflow.md#3-what-each-stage-writes-and-how-it-reaches-the-main-branch).
