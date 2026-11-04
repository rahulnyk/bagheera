---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Managers enforce QA review and tests first; the harness warns and records

## Context and problem statement

QA approves a feature's test cases (design doc 3, section 4), and Build commits failing tests before any code (PRD P5, [0034](0034-build-tests-first-and-show-criteria-results.md)). The stage check stops when an approval is missing ([0032](0032-stage-check-stops-and-records-overrides.md)), and CI warns when the tests-first commit is missing. Should the harness enforce these two, or leave them to people?

## Decision outcome

Set by the owner: both are strongly encouraged, and enforced at the process level by managers, not by the tool. A manager can insist on them and grant exceptions as they see fit.

* **QA's approval of test cases** never stops a stage command, in the test plan or in a revision. The stage check warns when it's missing, and the gap is recorded on the dashboard and in the weekly summary.
* **A missing tests-first commit** is a warning on the phase PR, and it is recorded on the dashboard and in the weekly summary.

The PM's approval of the acceptance criteria still counts as before: the stage check stops without it, with a recorded override possible.

### Consequences

* Good, because exceptions are a manager's call, made where the context is, not a tool setting.
* Good, because developers aren't blocked on a QA reviewer, and nothing that matters goes unseen.
* Bad, because the harness can't guarantee QA review or tests first; it depends on managers reading the dashboard.
* Neutral: the library is unaffected, since feature entries carry the criteria, not the test cases.

## More information

Detail in [design/developer-workflow.md, sections 4 and 6](../design/developer-workflow.md#4-approval). Amends [0032](0032-stage-check-stops-and-records-overrides.md), [0034](0034-build-tests-first-and-show-criteria-results.md) and [0036](0036-revise-with-one-command-minor-or-new-ticket.md).
