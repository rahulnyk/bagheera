# Bagheera

Delivery harness on Claude Code. Source of truth: `docs/prd.md`. Flag conflicts with it instead of guessing.

## Naming

- Keep a light Jungle Book theme: the product is Bagheera; themed names only for a few top-level things where a name genuinely helps, proposed to the owner first. Agents, commands, skills, files and workflows get plain descriptive names.
- Never use generic placeholder company names anywhere, in this or any other project. The placeholder GitHub org is `seeonee`.

## Working rules

- Simplest approach first: build it, test it, then improve. Don't design for rare corner cases up front unless they carry a grave security risk; name them in one line as accepted and move on. The owner isn't a purist.
- Fewest moving parts that meet the PRD's must-haves. Prefer configuring existing first-party and open-source tools; when building, say what was considered and why it didn't fit.
- Explain in plain language; take decisions one at a time with a recommendation.
- Record each decision the owner makes as a short ADR in `docs/decisions/` (MADR format).
