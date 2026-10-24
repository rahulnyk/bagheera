---
status: proposed
date: 2026-10-24
decision-makers: RN, pilot tech lead, pilot PM
---

# Adopt Spec Kit through an org preset, and use Spec Kit's artifact names where they overlap

## Context and Problem Statement

The architecture adopts GitHub Spec Kit and lists per-stage artifacts: `spec.md`, `architecture.md`, `plan.md` (2–6 phases), `testplan.md`, `demo.md`, all under `specs/<ticket-id>/`. Spec Kit (v1.2, Oct 2026) delivers commands to Claude Code as skills (`/speckit-specify`, `/speckit-plan`, `/speckit-tasks`, `/speckit-implement`, …), customizes through **presets** (template and command overrides), **extensions** (new commands) and **bundles**, and its `plan` step writes the *technical* plan to `plan.md` while `tasks.md` holds the phased breakdown. Its scripts hard-code `plan.md` as the technical-plan path. So "plan.md" means different things in the architecture and in Spec Kit.

## Decision Drivers

* Customize Spec Kit, don't fork it, so upgrades stay cheap
* PMs read the artifacts; names must be obvious
* `specs/<ticket-id>/` naming

## Considered Options

* A. Spec Kit names: `spec.md` (stage 1), `plan.md` + `data-model.md` + `contracts/` (stage 2, "architecture"), `tasks.md` (stage 3, phased plan), plus new `testplan.md` and `demo.md` from a `bageera` extension
* B. Architecture names: override the `plan` and `tasks` commands and scripts so they write `architecture.md` and `plan.md`
* C. Generate both and keep them in sync

## Decision Outcome

Chosen option: "A", because Spec Kit's `plan` and `tasks` already do what stages 2 and 3 need, and renaming them means overriding core scripts we'd then carry through every upgrade.

| Stage | Plugin command | Spec Kit underneath | Artifact |
| --- | --- | --- | --- |
| 1 Intake | `/feature start JIRA-123` | `/speckit-specify` (+ `/speckit-clarify`) | `spec.md` |
| 2 Architecture | `/feature architect` | `/speckit-plan` | `plan.md`, `data-model.md`, `contracts/`, `research.md` |
| 3 Plan | `/feature plan` | `/speckit-tasks` (preset: 2–6 phases, each behind a flag) | `tasks.md` |
| 4 Test plan | `/feature testplan` | `bageera` extension command | `testplan.md` |
| 7 Demo | `/feature demo` | `bageera` extension command | `demo.md` |

* `specs/<ticket-id>/`: the plugin sets `SPECIFY_FEATURE_DIRECTORY=specs/<ticket-id>` before calling Spec Kit, which its scripts honour; no fork.
* Constitution: the preset's constitution template tells `/speckit-constitution` to build `.specify/memory/constitution.md` from the wiki's `org/` pages, with links back to them.
* Front-matter on every artifact: `ticket`, `stage`, `phase`, `status` (architecture, Layer 4).

### Consequences

* Good, because almost all of Layer 2 is templates and one small extension.
* Bad, because it changes the architecture doc's names; the architecture doc needs a one-line update if you accept this.
* Bad, because preset and extension files must be re-checked on each Spec Kit upgrade (`specify artifact list --json` shows which layer wins).

### Confirmation

Phase 2 test: on the sample repo, `/feature start` through `/feature testplan` writes exactly the files above under `specs/<ticket-id>/`, and `specify artifact info template:plan-template --json` shows the `bageera` preset winning.

## Pros and Cons of the Options

### B. Architecture names

* Good, because matches the doc.
* Bad, because overrides core scripts (`setup-plan`, `common.sh` paths); highest upgrade cost.

### C. Both

* Bad, because two sources of truth for the same content.

## More Information

Checked against the Spec Kit repo on 2026-10-24 (`docs/guides/customization.md`, `docs/integrations/claude.md`, `scripts/bash/common.sh`).
