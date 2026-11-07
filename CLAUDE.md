# Bagheera

Delivery harness on Claude Code. Source of truth: `docs/prd.md`. Flag conflicts with it instead of guessing.

## Naming

- Keep a light Jungle Book theme: the product is Bagheera; themed names only for a few top-level things where a name genuinely helps, proposed to the owner first. Agents, commands, skills, files and workflows get plain descriptive names.
- Never use generic placeholder company names anywhere, in this or any other project. The placeholder GitHub org is `seeonee`.

## Working rules

- Simplest possible system first: build the smallest system that's useful on its own, test it, then add parts one at a time when real use shows they're needed. This is not "build every part of the full design in a simple form". Don't design for rare corner cases up front unless they carry a grave security risk; name them in one line as accepted and move on. The owner isn't a purist, and the PRD can be edited to simplify.
- Fewest moving parts that meet the PRD's must-haves. Prefer configuring existing first-party and open-source tools; when building, say what was considered and why it didn't fit.
- Explain in plain language; take decisions one at a time with a recommendation.
- Record each decision the owner makes as a short ADR in `docs/decisions/` (MADR format).
- No decision trail. Nothing is built yet, so there's no history to keep: git has it. When a decision replaces another, delete the old one as if it never existed, fold any change into the ADR it changes, and patch the whole repo to state the new decision. Title each ADR by what it decides. Never write that something was dropped, replaced, superseded, set aside or is no longer needed, and don't keep "Amended by" or "Replaces" lines or context that tells the history.
