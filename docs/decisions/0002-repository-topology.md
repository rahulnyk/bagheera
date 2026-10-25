---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Three repos: council-rock (wiki), pack (plugin marketplace), chil (monitoring)

## Context and Problem Statement

The architecture names an `org-wiki` repo (here `council-rock`) and "an internal marketplace repo" holding the plugin (here the `bageera` plugin in `pack`). It doesn't say where the Spec Kit preset, the reusable CI workflows or the monitoring config live. The plan and ADRs live in the project repo, `bageera`. Where should each piece live?

## Decision Drivers

* The wiki trust boundary depends on repo-level controls: CODEOWNERS, rulesets and auto-merge apply to a whole repository, not a folder
* Fewer repos means less for a platform team of 2 to own
* Every service repo must be able to call the CI workflows with one short file
* Plugin changes must be reviewed like production code (architecture, Governance)

## Considered Options

* A. Three repos: `seeonee/council-rock`; `seeonee/pack` (plugin, Spec Kit preset, reusable workflows, managed-settings source); `seeonee/chil` (or the platform team's existing infra repo)
* B. One monorepo for everything
* C. Build everything inside `bageera`

## Decision Outcome

Chosen option: "A", because the wiki needs its own repo for its trust rules to work, and the plugin, preset and reusable workflows change together and share one reviewer group.

### Consequences

* Good, because `council-rock` CODEOWNERS and rulesets are simple and can't be weakened by unrelated changes.
* Good, because service repos only add a 10-line caller workflow (`uses: seeonee/pack/.github/workflows/wiki-curate.yml@v1`).
* Bad, because a release of the plugin and of the reusable workflows must be versioned together (one tag, `v1.x`, covers both).
* Bad, because during development the `council-rock/` scaffold in `bageera` is only a template. Its trust rules can't be tested until it is pushed to its own repo.

### Confirmation

Phase 1 exit criteria include a ruleset test on the real `council-rock` repo (see plan, Phase 1 test plan T1.4).

## Pros and Cons of the Options

### B. Monorepo

* Good, because one place to look.
* Bad, because wiki auto-merge and CODEOWNERS rules would apply to plugin code too, or need path exceptions that are easy to get wrong.

### C. Everything in `bageera`

* Good, because one repo to create.
* Bad, because the wiki's trust boundary can't be enforced on a subfolder, and the plugin's reviewers would also own the wiki.

## More Information

Needs your call: the GitHub org name (`seeonee` is a placeholder) and whether I may create sandbox repos for testing. Depends on the GitHub assumption.
