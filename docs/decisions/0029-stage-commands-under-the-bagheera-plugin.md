---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# One command per stage under the `bagheera` plugin, and `/bagheera` for where you stand

## Context and problem statement

Commands use the PRD's stage names ([0003](0003-stage-names-from-the-prd.md)). Claude Code puts a plugin's name in front of its commands (`/plugin-name:skill-name`); a bare name works only when no other command uses it. Some stage names clash with Claude Code's own commands: `/plan` is built in. How should the commands be named?

## Considered options

* A. One command per stage under the plugin's name (`/bagheera:intake PAY-412`, `/bagheera:plan`, …), plus `/bagheera` for where the feature stands and what to do next
* B. One command with the stage as an argument (`/bagheera intake PAY-412`, `/bagheera plan`)
* C. Bare names, with different words where they clash (for example `/phases` for Plan)

## Decision outcome

Chosen option: A.

* The plugin is named `bagheera`. Each command is its own skill: `intake`, `plan`, `build`, `update-library` and `standards`.
* A skill named `bagheera` says where the feature stands and prints the exact next command (PRD P3).
* `/bagheera:build` takes no phase number; it builds the next phase ([0040](0040-build-phases-one-at-a-time.md)).
* Docs, the guide and the commands' own output always show the full `/bagheera:<command>` form.

### Consequences

* Good, because the names match the PRD's stages (P6), and typing `/bagheera:` lists every command.
* Good, because each command pre-approves only the tools it needs.
* Good, because a new joiner only has to remember `/bagheera`.
* Bad, because a bare `/intake` works while a bare `/plan` opens Claude Code's planning mode; showing the full names everywhere is the guard.
* Option C was ruled out because it breaks one set of stage names everywhere.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md). Built-in commands: Claude Code's [commands reference](https://code.claude.com/docs/en/commands); plugin skill naming: [skills](https://code.claude.com/docs/en/skills).
