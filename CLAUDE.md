# Bageera

SDLC harness on Claude Code. Plan: `docs/plan.md`. Decisions: `docs/decisions/` (MADR 4). Source of truth: `docs/architecture.md`; flag conflicts with it instead of guessing.

## Naming

- Use Jungle Book names from `docs/glossary.md` for repos, the plugin, agents, bots and apps. Keep skills, commands, hooks, scripts, workflows and files plainly named.
- Never use `acme` (or other generic placeholder company names) anywhere. The placeholder GitHub org is `seeonee`.
- When adding a new named part, add it to the glossary in the same change.

## Working rules

- Build as little as possible: prefer configuring existing open-source and first-party tools; when building, say what was considered and why it didn't fit.
- Record each significant choice as an ADR in `docs/decisions/` from `adr-template.md`.
