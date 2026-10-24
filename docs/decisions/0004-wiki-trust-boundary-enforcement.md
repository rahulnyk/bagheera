---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Enforce the wiki trust boundary with a ruleset, CODEOWNERS, a path-policy check and split curator PRs

## Context and Problem Statement

Agents may auto-merge into `features/` and `services/` after checks pass; `org/` and `decisions/` need CODEOWNERS approval (architecture, Layer 1 step 4). The architecture doesn't say how to enforce this, and leaves `raw/`, `index.md`, `CHANGELOG.md`, `CLAUDE.md` and `.github/` unassigned. Separately, stage 8 says "Feature entry and ADRs merged into council-rock — Automatic", which conflicts with ADRs needing approval.

## Decision Drivers

* An agent must not be able to change policy, even by mixing a policy edit into a routine PR
* Use GitHub's own controls, not a custom merge bot
* Routine updates must merge with no human in the loop

## Considered Options

* A. Ruleset on `main` (PR required, 0 general approvals, code-owner review required, required status checks) + CODEOWNERS on protected paths + a `path-policy` check that fails a bot PR mixing protected and unprotected paths + the curator opening two PRs when needed + GitHub native auto-merge on the unprotected one
* B. Separate repos for `org/`+`decisions/` and for agent-written pages
* C. A merge bot (e.g. Mergify) with path rules

## Decision Outcome

Chosen option: "A", because each piece is native GitHub and the path-policy check closes the "mixed PR" gap that CODEOWNERS alone leaves open.

Zones:

| Path | Who may merge | How |
| --- | --- | --- |
| `features/`, `services/`, `raw/` (add-only), `index.md`, `CHANGELOG.md` | Curator bot | Auto-merge after required checks |
| `org/`, `decisions/` | CODEOWNERS (`@seeonee/eng-standards`, `@seeonee/architecture`) | Human approval |
| `CLAUDE.md`, `.github/`, `CODEOWNERS` | `@seeonee/harness-platform` | Human approval |

Stage 8 is read as: the feature entry merges automatically; ADRs are proposed in a separate PR and wait for CODEOWNERS.

### Consequences

* Good, because the bot can't approve its own PR and can't widen its own permissions (`.github/` is owner-protected).
* Good, because `raw/` being add-only is a simple deterministic check (no modified or deleted files under `raw/`).
* Bad, because "0 approvals + code-owner review" is a ruleset combination we have seen used (e.g. LSST Phalanx) but not found in GitHub's own docs; it must be tested before we rely on it.
* Bad, because two PRs per merge when ADRs are involved means a little more noise.

### Confirmation

Phase 1 test T1.4 on the real `council-rock` repo: (1) bot PR touching only `services/` auto-merges; (2) bot PR touching `org/` waits for an owner; (3) bot PR touching both fails `path-policy`; (4) bot PR modifying a `raw/` file fails; (5) bot PR touching `.github/` waits for platform owners.

## Pros and Cons of the Options

### B. Separate repos

* Good, because the strongest separation.
* Bad, because links between policy and service pages cross repos, and lint has to read two repos.

### C. Merge bot

* Good, because expressive path rules.
* Bad, because another third-party app with write access to the wiki, which is the asset we're protecting.

## More Information

Depends on GitHub. Prompt-injection handling of curator input is a separate decision: [0010](0010-curator-untrusted-input.md).
