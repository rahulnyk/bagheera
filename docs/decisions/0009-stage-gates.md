---
status: proposed
date: 2026-10-24
decision-makers: RN, pilot tech lead
---

# A stage gate is an approved artifact on the default branch; a PreToolUse hook checks the default-branch copy

## Context and Problem Statement

Status lives in artifact front-matter (`status: draft | approved`) and the next command refuses to run until the prior gate is `approved`, enforced by a `PreToolUse` hook (architecture, Layer 2). If the hook reads the local file, anyone can type `approved` and skip the gate. Who approves, and what does the hook trust?

## Decision Drivers

* The approval must come from the named role (PM, tech lead, QA), not from the engineer running the command
* No new approval service
* Works offline enough not to block local work on flaky networks

## Considered Options

* A. CODEOWNERS per artifact (`specs/**/spec.md @acme/pm`, `specs/**/plan.md @acme/tech-leads`, `specs/**/testplan.md @acme/pm @acme/qa`); ruleset requires code-owner review; the hook reads `status` from `origin/<default>` (after a fetch), not the working tree
* B. The hook reads the local file
* C. The hook queries the PR's review state through the GitHub API

## Decision Outcome

Chosen option: "A", because it reuses GitHub's review rules, and an artifact only reaches the default branch with `status: approved` if its owner approved the PR that set it.

### Consequences

* Good, because PMs approve in a rendered PR, as the architecture wants.
* Bad, because PMs and QA need GitHub accounts with review rights (seat cost; see plan open questions).
* Bad, because the hook needs a `git fetch`; when offline it allows the command with a warning rather than blocking, and CI re-checks on the PR.

### Confirmation

Phase 2 test: `/feature architect` is refused while `spec.md` is `draft` on `main`; editing the local copy to `approved` still refuses; after a PM-approved merge it proceeds.

## Pros and Cons of the Options

### B. Local file

* Bad, because the gate is self-service.

### C. GitHub API per command

* Good, because precise.
* Bad, because more code and a network call on every command; A gives the same guarantee through the merge.

## More Information

The hook matches the `Skill` tool and the `acme-sdlc` plugin's `feature` skill. Depends on GitHub.
