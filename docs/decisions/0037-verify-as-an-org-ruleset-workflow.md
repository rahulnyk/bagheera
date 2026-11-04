---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# Run Verify as an org ruleset workflow; put the results table and Close on existing scheduled jobs

## Context and problem statement

Verify (the PR review, the feature folder check and criteria coverage) has to run on every PR in every onboarded repo. The criteria results table needs the repo's test results after its CI finishes, and Close needs the PM's `/approved demo` comment. GitHub org rulesets can require a workflow from a central repo on every PR in the repos they target, with no file in those repos, but such workflows run only when a PR is opened or updated. The owner is wary of moving parts and of depending on other teams.

## Considered options

* A. An org ruleset requires the Verify workflow from `seeonee/bagheera`; the results table rides on the 15-minute dashboard sync, and Close on the hourly curation run
* B. A one-line workflow file in each repo that calls a shared workflow in `seeonee/bagheera`
* C. Everything on the 15-minute schedule, posting commit statuses

## Decision outcome

Chosen option: A. The ruleset targets repos by a property (`bagheera: on`) that the platform team can set, so after a one-time setup by the GitHub org owners, onboarding a repo needs nobody outside the platform team. The ruleset covers only each repo's default branch, since ruleset workflows block direct pushes to the branches they cover. The Verify workflow is written so it can also be called from a one-line file in a repo; if the ruleset isn't ready, the pilot can start that way and switch without rewriting.

A table of the harness's moving parts and outside dependencies is kept in the design index and updated with every design doc.

### Consequences

* Good, because nothing is added to any product repo, and Verify changes in one place.
* Good, because no new jobs: the two event-driven needs reuse scheduled jobs that already exist.
* Good, because the org owners' part is one-time, about an hour.
* Bad, because the results table and Close lag up to 15 minutes and an hour respectively.
* Bad, because org rulesets for private repos need GitHub's Team or Enterprise plan.

## More information

Detail in [design/developer-workflow.md, section 9](../design/developer-workflow.md#9-what-runs-locally-and-what-runs-in-ci). Source: GitHub docs, "Require workflows to pass before merging" in [available rules for rulesets](https://github.com/github/docs/blob/main/content/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets.md).

Amended by [0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md): in the pilot, the developer runs Verify on their laptop with `/bagheera:verify`, and the pilot repos' branch rules require its mark; the org ruleset and the scheduled jobs start at rollout.
