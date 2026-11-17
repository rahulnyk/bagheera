# 7. Only the architecture council changes standards

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

Standards live in the wiki (S1), and wiki changes reach it from feature PRs (ADR 3). Can Bagheera change a standard?

## Considered Options

* Bagheera never changes standards; the council edits `standards/` directly in the wiki repo
* Standard changes ride in feature PRs, with council review

## Decision Outcome

Chosen option: Bagheera never changes standards, because a feature PR is reviewed by a tech lead, not the council.

### Consequences

* Good: `standards/` is a protected path for the developer plugin, the merge check rejects it, and CODEOWNERS requires the council in the wiki repo.
* Bad: a developer who finds a wrong standard tells the council rather than fixing it in their PR.
