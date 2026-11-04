---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# CI signs in to Claude by exchanging each run's GitHub identity

## Context and problem statement

PRD D3: CI uses short-lived credentials, with no long-lived keys in repos. Verify runs inside each onboarded product repo, through the org ruleset ([0037](0037-verify-as-an-org-ruleset-workflow.md)), and any workflow a developer writes in that repo can read the secrets available there. The curation run and the dashboard sync run in `seeonee/bagheera`. How does CI sign in to Claude?

## Considered options

* A. Workload identity federation: each run's GitHub identity token is exchanged in the Claude Console for access that expires in minutes, under rules that admit only our workflows
* B. A Claude API key as an organisation-wide GitHub secret
* C. Claude through a cloud provider, such as Amazon Bedrock or Google Cloud, with the provider's own identity federation

## Decision outcome

Chosen option: A.

* **Nothing stored.** Workflows get GitHub's signed identity token for the run and the Claude Code GitHub Action exchanges it for short-lived Claude access.
* **Only our workflows qualify.** One rule admits Verify from `seeonee/bagheera` at `release`, in whichever repo it runs; another admits the scheduled jobs that run in `seeonee/bagheera` itself. A workflow someone writes in a product repo matches neither.
* **CI has its own Console workspace,** billed per use and reported apart from the seats laptops use (PRD D4).
* **One-time setup by the Claude admin:** GitHub as a trusted issuer, a service account, the workspace and the two rules.

### Consequences

* Good, because there is no Claude key to leak, rotate or revoke.
* Good, because it is built into the Claude Code GitHub Action we already use.
* Bad, because it needs a Claude Console organisation for CI alongside the Team or Enterprise plan for laptops.
* Bad, because the rule for Verify relies on GitHub naming the called workflow in the token of a ruleset-required run; this is tested once in Foundations. If it doesn't, the rule falls back to the org's repos, and the workspace's spend cap bounds any misuse.

## More information

Detail in [design/distribution-security-and-cost.md, section 3](../design/distribution-security-and-cost.md#3-ci-credentials). Sources: Claude platform docs on [workload identity federation](https://platform.claude.com/docs/en/manage-claude/workload-identity-federation) and [its GitHub Actions setup](https://platform.claude.com/docs/en/manage-claude/wif-providers/github-actions).
