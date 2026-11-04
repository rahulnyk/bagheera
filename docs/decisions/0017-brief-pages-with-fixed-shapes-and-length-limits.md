---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Keep pages brief without losing context: fixed shapes and length limits

## Context and problem statement

The owner set a principle: actively fight verbosity in the knowledge library, but not at the cost of context. A decision without its context is as good as lost; a decision buried in long reasoning and detailed logs is just as unusable. How do we enforce that?

## Considered options

* A. Guidance only: writing rules for the knowledge agent and a reviewer checklist
* B. A fixed shape per page type with minimum and maximum lengths, checked automatically, plus writing rules
* C. Two layers per page: a short summary that sessions load, and an unlimited details section

## Decision outcome

Chosen option: B. Every page type has required sections with limits; the check fails on missing or empty sections and on pages well over their limit; logs, transcripts and long code are rejected; detail is linked through the source footnotes ([0012](0012-record-provenance-per-statement.md)) rather than copied.

### Consequences

* Good, because context can't be dropped (required sections) and sprawl can't creep in (limits).
* Good, because the full reasoning is one click away in its source.
* Bad, because the starting limits are guesses and will need tuning in the pilot.

## More information

Shapes, limits and writing rules: [design/knowledge-library.md, section 4](../design/knowledge-library.md#shape-and-length).

Amended by [0085](0085-six-header-fields-the-pilot-grows-the-schema.md): the shapes are starting templates in the schema, required by the format check; the pilot adds or drops sections by PR.
