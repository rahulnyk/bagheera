# 5. Developer path as a Claude Code plugin

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

Every developer, in any repo, must follow the same path from approved work to merged code, with pre-flight checks and hard blocks (P1–P9, S4).

## Considered Options

* A Claude Code plugin: commands, hooks, and a pre-flight script
* A desktop app or wrapper around Claude Code
* Prompt templates and a runbook

## Decision Outcome

Chosen option: the plugin, from the org's marketplace with auto-update, because it runs where developers already work and hooks can block actions outright. Each repo sets its default branch in `.bagheera/config.yml`.

### Consequences

* Good: the same commands and blocks on every machine, updated automatically (P8).
* Bad: blocks run on the developer's machine, so a developer could remove them until they are locked centrally (S7).
