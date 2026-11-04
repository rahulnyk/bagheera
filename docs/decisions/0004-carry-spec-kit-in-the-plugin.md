---
status: accepted
date: 2026-10-27
deciders: RN (owner)
---
# Carry Spec Kit in the plugin rather than in each repo

## Context and problem statement

Spec Kit normally installs into each repo (`specify init`, which writes `.specify/` and `.claude/skills/speckit-*`). The owner wants the least setup possible, leans towards carrying Spec Kit in the plugin, and is open to suggestions. Commands must use the PRD's stage names ([0003](0003-stage-names-from-the-prd.md)).

## Considered options

* A. Commit Spec Kit to each repo, with central update PRs (Spec Kit's normal model)
* B. Carry a pinned copy of Spec Kit's templates and prompts inside the plugin, as our own stage commands with PRD names
* C. Carry a pinned Spec Kit in the plugin and copy it into each repo at session start, kept out of git

## Decision outcome

Chosen option: B. We copy Spec Kit's templates, command instructions and helper scripts into the plugin and adapt them into our own stage commands with the PRD's names. We record which Spec Kit version we copied from and update by hand when a new release is worth taking. Spec Kit is MIT-licensed; the plugin keeps its licence notice. Option C was set aside because its commands would still be named `/speckit-…`. How the copy is organised and kept up to date is settled in design doc 3.

### Consequences

* Good, because there is one install, one version everywhere (PRD D1) and nothing to set up per repo (P4).
* Good, because commands use the PRD's stage names ([0003](0003-stage-names-from-the-prd.md)).
* Good, because we keep Spec Kit's method and templates, which is most of its value.
* Bad, because we don't get Spec Kit's releases or community add-ons automatically; updates are a deliberate copy.

### Conflict with the PRD (flagged)

PRD §8 names "GitHub Spec Kit … customised through an org preset and an org extension" as a preferred building block. With this decision we use Spec Kit's content, not Spec Kit as installed software. The PRD's wording should be updated to match.

## More information

Refined by [0028](0028-adapt-spec-kit-once-and-review-it-quarterly.md): Spec Kit's helper scripts are not copied after all, and its changes are reviewed once a quarter. Detail in [design/developer-workflow.md, section 1](../design/developer-workflow.md#1-where-the-stage-commands-come-from).
