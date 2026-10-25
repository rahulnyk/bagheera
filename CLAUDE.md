# Bagheera

Delivery harness on Claude Code. Source of truth: `docs/prd.md`. Flag conflicts with it instead of guessing.

## Naming

- Keep a light Jungle Book theme: the product is Bagheera; themed names only for a few top-level things where a name genuinely helps, proposed to the owner first. Agents, commands, skills, files and workflows get plain descriptive names.
- Never use generic placeholder company names anywhere, in this or any other project. The placeholder GitHub org is `seeonee`.

## Working rules

- Fewest moving parts that meet the PRD's must-haves. Prefer configuring existing first-party and open-source tools; when building, say what was considered and why it didn't fit.
- Explain in plain language; take decisions one at a time with a recommendation.
- Record each decision the owner makes as a short ADR in `docs/decisions/` (MADR format).
