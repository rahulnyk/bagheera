---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# Build tests first, check criteria coverage in CI, and show results by criterion

## Context and problem statement

PRD P5: Build "writes failing tests from the test plan before any code, and is done when they pass". PRD C4: acceptance criteria have stable IDs reused as automated test names, "so the PM can see which criteria pass". Criteria get IDs such as `PAY-412-AC-3`, written in test names as `PAY_412_AC_3`. How much should CI check, and how do results reach the PM?

## Considered options

* A. The build command works tests first and commits the failing tests before any code; CI fails a phase PR when one of its criteria has no test, warns when the tests-first commit is missing, and posts a table of results by criterion
* B. A, plus proof in CI: run the new tests against the code before the change and require them to fail
* C. Leave it to the build command; CI only reports results by criterion

## Decision outcome

Chosen option: A. `/bagheera:build <phase>` writes the phase's tests from the test plan, runs them, confirms they fail for the expected reason, and commits them first; then it writes code until they pass. On each phase PR, CI checks that every criterion assigned to the phase has a test carrying its ID, warns if the tests-first commit is missing, and keeps one PR comment with a table of each criterion, its tests and whether they pass. The dashboard shows the same.

### Consequences

* Good, because the PM sees, per criterion, whether it passes (C4), and a criterion without a test is caught.
* Good, because the build follows P5's order without per-language machinery in CI.
* Bad, because tests-first is shown, not proven: a warning, in keeping with soft enforcement.
* Bad, because pass or fail per criterion needs each repo's CI to save its test results in JUnit format. Onboarding a repo includes that one configuration change; without it, the table shows coverage only.

## More information

Detail in [design/developer-workflow.md, section 6](../design/developer-workflow.md#6-build).

Amended by [0038](0038-managers-enforce-qa-review-and-tests-first.md) (a missing tests-first commit is also recorded on the dashboard; managers enforce it) and [0040](0040-build-phases-one-at-a-time.md) (phases one at a time; no phase number).

Amended by [0076](0076-criteria-ids-in-test-names-results-in-the-pr.md): in the first system, no trailer, CI checks or JUnit table; the PR lists each criterion, its tests and their result from Build's run.
