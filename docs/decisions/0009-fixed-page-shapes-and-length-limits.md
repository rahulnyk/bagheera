---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Keep pages brief without losing context: fixed shapes and length limits

## Context and problem statement

The owner set a principle: actively fight verbosity in the knowledge library, but not at the cost of context. A decision without its context is as good as lost; a decision buried in long reasoning and detailed logs is just as unusable. How do we enforce that?

## Considered options

* A. Guidance only: writing rules for the library writer and a reviewer checklist
* B. A fixed shape per page type with minimum and maximum lengths, checked automatically, plus writing rules
* C. Two layers per page: a short summary that sessions load, and an unlimited details section

## Decision outcome

Chosen option: B.

* Each page type starts from a template of required sections, each with a limit.
* The format check fails on missing or empty sections and on pages well over their limit, and rejects logs, transcripts and long code.
* Detail stays where it was written, such as the approved spec, the plan or the PR, and the page links to it.
* The pilot adds or drops sections, and tunes the limits, by a PR to the schema.

### Consequences

* Good, because context can't be dropped (required sections) and sprawl can't creep in (limits).
* Good, because the full reasoning is one click away in its source.
* Bad, because the starting limits are guesses and will need tuning in the pilot.

## More information

Shapes, limits and writing rules: [design/knowledge-library.md](../design/knowledge-library.md).
