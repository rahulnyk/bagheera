---
status: superseded by [0068](0068-no-spend-caps-until-after-the-pilot.md)
date: 2026-11-04
deciders: RN (owner)
---
# Cap CI spend per job and per month, with Claude's own settings

## Context and problem statement

PRD D4: CI spend is capped per job and reported separately from seat usage. CI exists from rollout ([0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md)), and its Claude usage goes to its own Console workspace ([0061](0061-ci-signs-in-to-claude-with-workload-identity-federation.md)). How is it capped and reported?

## Considered options

* A. Claude Code's own per-run caps in each workflow, one monthly spend limit on the CI workspace, and the Console's cost page for reporting
* B. A, plus one workspace per team, to cap and report CI spend by team

## Decision outcome

Chosen option: A.

* **Per job:** each workflow runs Claude Code with `--max-budget-usd` and `--max-turns`.
* **Per month:** one spend limit on CI's Console workspace, set with headroom.
* **Reported** on the Console's cost page for that workspace, apart from seats, which claude.ai reports.
* **The numbers** are set before rollout, from what Verify and library-update runs cost in the pilot.

### Consequences

* Good, because it is settings only, in tools already in use.
* Bad, because the per-run cap works from Claude Code's own estimate, so a run can pass it slightly.
* Bad, because if the monthly limit is reached, Verify can't run and PRs wait until the platform team raises it.

## More information

Detail in [design/distribution-security-and-cost.md, section 4](../design/distribution-security-and-cost.md#4-spend-limits-and-reporting).
