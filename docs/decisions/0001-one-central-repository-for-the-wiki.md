# 1. One central repository for the wiki

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

PMs draft across services before any repo is chosen, and every Claude Code session needs org-wide context (K1, K9). Where does the wiki live?

## Considered Options

* One central Git repository, `seeonee/wiki`
* A `docs/` folder in each service repo, gathered by a harvester

## Decision Outcome

Chosen option: one central repository, because PMs, the portal and Claude read one place, and no harvester has to be built or run.

### Consequences

* Good: services, dependencies, features, decisions, standards and PRDs sit in one graph; git history is the change log (K3).
* Bad: wiki changes made in service repos must be carried across (ADR 3).
