---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# One GitHub App, its key shared with the onboarded repos

## Context and problem statement

From rollout, one GitHub App gives the CI jobs their GitHub access ([0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md)). GitHub can't exchange a run's identity for an App's access, so the App needs a stored private key; each run uses it to get a token that lasts an hour. Verify runs inside each onboarded product repo, where any workflow can read the secrets shared with that repo. Where does the key live?

## Considered options

* A. Split by power: Verify posts with the run's built-in token and reads with a second, read-only App; the writing App's key stays in `seeonee/bagheera`
* B. One App, its key an organisation secret shared with every onboarded repo
* C. No stored GitHub keys, through a token exchange service such as Octo STS

## Decision outcome

Set by the owner: B, the simplest. The key is an organisation-level Actions secret available to the repos whose `bagheera` property is `on`, and every CI job uses the same App.

### Consequences

* Good, because it is one App, one secret and one install.
* Good, because Verify keeps posting through the App, as design doc 4 already says.
* Bad, because a workflow written in an onboarded repo could read the key and act as Bagheera. The owner judged this rare enough to accept: it takes a deliberate, reviewed change to a repo's workflows, and anything the App does is on record in GitHub. It can be tightened later if it ever matters.

## More information

Detail in [design/distribution-security-and-cost.md, section 3](../design/distribution-security-and-cost.md#3-where-the-jobs-run). Amends [0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md), which left the key's home to the rollout plan.
