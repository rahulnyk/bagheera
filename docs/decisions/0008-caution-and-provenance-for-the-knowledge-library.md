---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Err on the side of caution and track where every piece of knowledge came from

## Context and problem statement

Every session loads the knowledge library, so a wrong or planted statement reaches everyone (PRD K5). PRD K4 lets agents merge service and feature pages after automated checks. How much should we trust automatic updates?

## Considered options

* Let checked agent updates merge on their own (PRD K4 as written)
* Be cautious: record the source of every piece of knowledge, never trust external documents blindly, and require people where trust is not established

## Decision outcome

Chosen option: be cautious. The library records the provenance of its content, and external documents (tickets, PR descriptions, pasted or linked documents) are never trusted blindly. The exact provenance model, trust levels and merge rules are designed in design doc 1.

### Consequences

* Good, because a reader, and Claude, can tell what was approved by a person and what was inferred.
* Bad, because more human review may slow updates against the "within a day" target in PRD §7. Design doc 1 weighs this.
