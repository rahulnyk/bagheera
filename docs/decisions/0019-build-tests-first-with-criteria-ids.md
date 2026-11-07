---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Build tests first, with criteria IDs in test names and results listed in the PR

## Context and problem statement

PRD P5: Build writes failing tests from the acceptance criteria before any code, and is done when they pass. PRD C4: criteria have stable IDs reused as test names, so the PM can see which criteria pass. Nothing runs in CI. How does Build do this, and how do results reach the PM?

## Considered options

* A. Tests first in Build; criteria IDs in test names; Build lists each criterion, its tests and their result in the PR
* B. A, plus CI checks that every criterion has a test and that tests came first, and a results table from each repo's JUnit output
* C. The list in the PR, without IDs in test names

## Decision outcome

Chosen by the owner: A.

* Each criterion has an ID, `<TICKET>-AC-<n>`, such as `PAY-412-AC-3`, never reused or changed. In test names it's written `PAY_412_AC_3`, which every common test framework accepts.
* For each phase, Build writes tests for the phase's criteria, runs them, confirms they fail for the expected reason, and commits them on their own. Then it writes code until they pass.
* The PR's description lists each criterion, its tests, and whether they passed in Build's own run.

### Consequences

* Good, because the PM sees, per criterion, whether it passes (C4), with nothing in CI and nothing changed in teams' own CI.
* Good, because the IDs in test names let a coverage check and a results table be added later without renaming tests.
* Bad, because the results are as of Build's last run, not the repo's CI, and nothing checks that tests came first; the commit order on the PR shows it.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
