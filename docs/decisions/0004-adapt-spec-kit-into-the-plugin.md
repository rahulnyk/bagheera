---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# Adapt Spec Kit into the plugin once, and review its changes each quarter

## Context and problem statement

Spec Kit normally installs into each repo (`specify init`, which writes `.specify/` and `.claude/skills/speckit-*`), and releases every few days, mostly changes to its installer, extensions and presets. The owner wants the least setup possible, and commands must use the PRD's stage names ([0003](0003-stage-names-from-the-prd.md)). How do we use Spec Kit, and how do we follow its changes?

## Considered options

* A. Commit Spec Kit to each repo, with central update PRs (Spec Kit's normal model)
* B. Adapt its templates and command instructions into the plugin once, as our own stage commands, and review its changes each quarter
* C. Carry a pinned Spec Kit in the plugin and copy it into each repo at session start, kept out of git

## Decision outcome

Chosen option: B.

* We copy Spec Kit's spec, plan and tasks templates and the instructions of `specify`, `clarify`, `plan`, `tasks` and `implement`, and adapt them into our stage commands under the PRD's names.
* One short script of our own finds or creates the ticket's branch and folder. Spec Kit's helper scripts, extension hooks and support for other AI tools aren't copied.
* We record the Spec Kit version copied and which of our files came from which of theirs, and keep its MIT licence notice.
* Once a quarter, a platform engineer reads Spec Kit's changes to those files since the recorded version and ports what helps.

Option C would still name the commands `/speckit-…`.

### Consequences

* Good, because there is one install, one version everywhere and nothing to set up per repo (PRD P4, D1).
* Good, because we keep Spec Kit's method and templates, which are most of its value, without its machinery.
* Bad, because improvements reach us up to a quarter late, and only when someone ports them.
* Bad, because our commands drift from Spec Kit's over time, so each review takes judgement, not just copying.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
