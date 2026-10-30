---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Five shared dashboard views, with "Waiting on me" first

## Context and problem statement

PRD V1 asks for a list of stories filterable by team, PM, stage, status and "waiting on me". The dashboard is an org-level GitHub Project ([0051](0051-dashboard-on-an-org-github-project.md)), whose saved views appear as tabs; anyone can also save their own. Which views ship as standard?

## Considered options

* A. Five shared views, one job each: Waiting on me (the default), All stories, By stage, Needs attention, Shipped
* B. One view per audience: PM, tech lead, QA, manager, leadership
* C. A single table; people filter and save their own views

## Decision outcome

Chosen option: A. The platform team sets up the five views once, shared with everyone, and "Waiting on me" is the first tab. Anyone can filter further and save personal views.

### Consequences

* Good, because everyone starts from what's waiting on them, with little to learn.
* Good, because five views cover V1's filters, managers' markers and leadership's "where is X" question.
* Bad, because no view is tailored to one role; people who want one save it themselves.

## More information

Detail in [design/status-and-dashboard.md, section 4](../design/status-and-dashboard.md#4-views-and-filters).
