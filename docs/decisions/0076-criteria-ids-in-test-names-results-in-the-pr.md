---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Criteria IDs in test names, results listed in the PR

## Context and problem statement

[0034](0034-build-tests-first-and-show-criteria-results.md) and [0038](0038-managers-enforce-qa-review-and-tests-first.md) mark the tests-first commit with a trailer, warn in CI when it's missing, check in CI that every criterion has a test named with its ID, and keep a results table on the PR from each repo's JUnit output. The first system ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)) has no CI and no dashboard. How much of this does it keep?

## Considered options

* A. IDs in test names; the criteria, tests and results listed in the PR; no trailer, CI checks or JUnit
* B. The list in the PR, without IDs in test names
* C. As designed

## Decision outcome

Chosen by the owner: A.

* Build commits each phase's failing tests first, then the code.
* Each test's name carries its criterion's ID, such as `test_PAY_412_AC_3_refund_rejected_after_30_days` (PRD C4).
* The PR's description lists each criterion, its tests, and whether they passed in Build's own run.
* No `Bagheera-Step: tests-first` trailer, no tests-first warning, no coverage check, and no JUnit setting for teams.

### Consequences

* Good, because nothing runs in CI and teams change nothing in their own CI.
* Good, because the IDs in test names let the coverage check and results table be added later without renaming tests.
* Bad, because the results are as of Build's last run, not the repo's CI, and nothing checks that tests came first; the commit order on the PR shows it.

## More information

Amends [0034](0034-build-tests-first-and-show-criteria-results.md) and [0038](0038-managers-enforce-qa-review-and-tests-first.md) for the first system; the coverage check and results table are additions in [plan.md, section 3](../plan.md#3-additions).
