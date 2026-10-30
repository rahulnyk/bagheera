---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# Another iteration through `/bagheera:revise`; PM approval optional for minor revisions; major changes become a new ticket

## Context and problem statement

A demo, or anything else after planning has merged, can turn up work that needs more development. The developer should be able to go back to Bagheera and start another iteration. The owner doesn't want PMs chased for approvals of minor revisions, and wants major changes handled as new tickets.

## Considered options

* A. One command, `/bagheera:revise "<feedback>"`, that works out which documents change and opens one revision PR
* B. Re-run the affected stage commands one by one, each opening or updating a revision PR
* C. A new ticket for any feedback that needs development

## Decision outcome

Chosen option: A, with two rules set by the owner.

* **Minor or major.** `/bagheera:revise` classifies the change and gives its reasons; the developer confirms. A minor change stays within the ticket's approved scope. A major change, such as a new capability or a change to interfaces other tickets rely on, is not revised: the command drafts a new, linked ticket for the developer and PM to create in Jira.
* **PM approval optional for minor revisions.** In a minor revision PR, the PM is mentioned but their approval isn't required; the stage check doesn't wait for it, and it isn't an override. The PM's next `/approved demo` counts as their approval of every minor revision since the last demo. Approvals from other roles (tech lead for the plan, QA for test cases) stay as in [0032](0032-stage-check-stops-and-records-overrides.md).

A revision PR (`PAY-412/revision-1`, `-2`, …) holds the changed planning documents. Numbering carries on: new phases, criteria and questions continue from the last ones. Once it merges, `/bagheera:build` builds the new phase and `/bagheera:demo` runs again. The feature stays open until the PM approves a demo.

### Consequences

* Good, because another iteration is one command, and PMs aren't chased for changes they asked for themselves.
* Good, because the PM still confirms the result, at the next demo, so the library's feature entry is built only from approved work.
* Good, because scope stays contained: major changes become their own tickets.
* Bad, because the minor-or-major call is a judgement; Claude proposes it, the developer decides, and the PM can object on the revision PR.
* Bad, because a minor revision's PM-owned changes are unapproved until the next demo; the dashboard shows them as waiting for the demo.

## More information

Detail in [design/developer-workflow.md, section 8](../design/developer-workflow.md#8-another-iteration).

Amended by [0038](0038-managers-enforce-qa-review-and-tests-first.md): QA's review of new test cases is encouraged, not required by the tool.
