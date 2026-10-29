---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Propose decisions as soon as they are approved; write the feature entry at Close

## Context and problem statement

PRD K3 has an agent propose service page, feature entry and decision updates on every merged PR. PRD §5 adds the feature summary and accepted decisions at Close. When should a feature's knowledge enter the library?

## Considered options

* A. Everything at Close: the feature entry and its decisions
* B. A draft feature entry from Intake, updated at every stage until Close
* C. The feature entry at Close; decision records proposed as soon as the stage that approved them merges

## Decision outcome

Chosen option: C. Product rules are proposed after Intake, technical decisions after Architecture, service pages update from each merged build PR, and the feature entry is written at Close from the approved documents.

### Consequences

* Good, because decisions, the most reusable knowledge, reach other teams weeks before the feature ships.
* Good, because the library holds no half-finished feature entries.
* Bad, because a decision that changes during the build needs a second, superseding record and a second approval.

## More information

Detail in [design/knowledge-library.md, section 5](../design/knowledge-library.md#5-what-each-stage-adds).

Under [0030](0030-one-planning-pr-per-feature.md), a feature's spec and architecture merge together in one planning PR, so the decisions from both are proposed when that PR merges, after the test plan is approved.
