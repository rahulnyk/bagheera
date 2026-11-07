---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Use the PRD's stage names for commands and documents

## Context and problem statement

Spec Kit's words clash with the PRD's: Spec Kit's `plan` is the PRD's Architecture stage, and its `tasks` is the PRD's Plan stage. PRD P6 asks for plain, consistent terms across commands, documents and the guide.

## Considered options

* Spec Kit's words (specify, plan, tasks, implement), and change PRD §5 to match
* The PRD's words (Intake, Architecture, Plan, Test plan, Build, Verify, Demo, Close)

## Decision outcome

Chosen option: the PRD's stage names, with a light Jungle Book touch where it helps (for example in the plugin's name, which prefixes every command). Command names are in [0029](0029-stage-commands-under-the-bagheera-plugin.md).

### Consequences

* Good, because people see one set of stage names everywhere: commands, documents, the dashboard and the guide.
* Bad, because Spec Kit's own command names can't be exposed as-is. See [0004](0004-carry-spec-kit-in-the-plugin.md).
