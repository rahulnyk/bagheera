---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Record provenance per statement for agent-written knowledge

## Context and problem statement

The owner chose caution and provenance for the knowledge library ([0008](0008-caution-and-provenance-for-the-knowledge-library.md)). Every session loads the library, so for any statement we need to know where it came from and whether a person stood behind it. How fine-grained should provenance be?

## Considered options

* A. Per page: front matter lists the page's sources and who last verified it
* B. Per statement: every statement carries a footnote with its source and trust level, enforced by a check
* C. Per section: each section ends with a list of its sources

## Decision outcome

Chosen option: B for everything the knowledge agent writes, and A for pages written by their owners (standards, and decision records written by people). Four trust levels: owner-approved, approved work, inferred, external. External text is never stored as a statement.

### Consequences

* Good, because a check can enforce that nothing external is trusted blindly.
* Good, because people and Claude can tell settled statements from inferred ones.
* Bad, because agent-written pages carry many footnotes.

## More information

Detail in [design/knowledge-library.md, section 1](../design/knowledge-library.md#1-provenance-and-trust).
