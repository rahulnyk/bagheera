---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Adapt Spec Kit's templates and method into the plugin

## Context and problem statement

GitHub Spec Kit has a proven method and templates for specs, plans and phased tasks. Do we use it as it is, or adapt it?

## Decision outcome

Chosen by the owner.

* The plugin adapts Spec Kit v1.2.5's spec, plan and tasks templates, and its `specify`, `clarify`, `plan`, `tasks` and `implement` instructions, into our commands under our stage names. It keeps Spec Kit's MIT notice and a record of which file came from which.
* Its helper scripts, extensions and support for other AI tools aren't copied.
* Once a quarter, a platform engineer reads Spec Kit's changes to those files and ports what helps.

### Consequences

* Good, because our commands are plain and our own, built on a proven method.
* Bad, because upstream improvements reach us only through the quarterly review.
