---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Plain, indicative names, with Jungle Book names for individual components

## Context and problem statement

PRD §8 asks for a light Jungle Book theme: themed names only for a few top-level things, plain names for agents, commands, skills, files and workflows. Appendix B records that the first attempt had too many themed names.

## Considered options

* Themed names only for the product
* Indicative names everywhere, plus Jungle Book names for individual components
* Jungle Book names for everything

## Decision outcome

Chosen option: indicative names, with Jungle Book names allowed for individual components. Each themed name is proposed to the owner first and recorded in the naming register in [design/README.md](../design/README.md#naming-register). The real GitHub org is not known yet; `seeonee` remains the placeholder.

### Consequences

* Good, because a few memorable names help people talk about the main components.

### Conflict with the PRD (flagged)

PRD §8, Appendix B and the repo's CLAUDE.md say agents, commands, skills, files and workflows get plain names. Proposed reconciliation, to confirm: themed names only for a handful of top-level components (around five), each shown with a plain description; commands, files, workflows and skills keep plain names.
