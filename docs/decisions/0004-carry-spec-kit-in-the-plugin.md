---
status: proposed
date: 2026-10-26
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

Owner's leaning: carry it in the plugin (B or C). To be confirmed in design doc 3 after comparing the options in detail.

### Consequences

* Good, because there is one install, one version everywhere (PRD D1) and no per-repo setup (P4).
* Bad, because with PRD names we no longer use Spec Kit as installed software with a preset and an extension, which is the PRD's preferred building block (§8). Upgrades become a deliberate re-copy. This is flagged for confirmation.
