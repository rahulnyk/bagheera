---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Build all of Layer 1, including the curator and lint, in Phase 1

## Context and Problem Statement

The architecture's rollout puts the wiki schema, `org/` pages and DeepWiki bootstrap in weeks 1–2, and the wiki curator (`baloo`) on merge and nightly lint in weeks 7–8 ("Compounding"). Your Task 2 builds the whole of Layer 1 first. Which order?

## Decision Drivers

* The wiki is input to every later stage (`SessionStart`, constitution, architect)
* Curator quality needs time and real merged PRs to tune
* Avoid building on inputs that don't exist yet

## Considered Options

* A. Phase 1 builds all of Layer 1; curation runs from week 2 on any merged PR, and reads `specs/<ticket>/` once Phase 2 produces them
* B. Follow the architecture: curator and lint in weeks 7–8

## Decision Outcome

Chosen option: "A", because the curator works on any merged PR (diff + description) and gets five extra weeks of tuning on real merges; `specs/` input is optional until Phase 2.

### Consequences

* Good, because the wiki is current before the pilot starts depending on it.
* Bad, because Phase 1 grows from about 2 weeks to about 3 weeks for 2 engineers; the plan's later phases shift by one week, or Phase 4 shrinks because curation is already done.
* Bad, because curation CI spend starts earlier.

### Confirmation

Phase 1 exit criteria in the plan.

## More Information

This is a deviation from the architecture's rollout, flagged for your review.
