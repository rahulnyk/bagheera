---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Six header fields to start, checked against a schema the pilot grows

## Context and problem statement

PRD K2 asks for pages that follow a schema a machine can check, and K10 for a format that stays portable; the Open Knowledge Format is a candidate. Bagheera needs a few header fields to find pages and label them. How much header does a page carry, and when is it defined?

## Considered options

* A. A full typed header per page type, defined before any page exists
* B. A few fields Bagheera reads, in a schema the pilot grows
* C. Free-form pages during the pilot, with the schema written at its end

## Decision outcome

Chosen by the owner: B. Page types and their metadata are defined from the pilot itself.

* Every page has a YAML header with a required `type`, so it stays compatible with the Open Knowledge Format without depending on a draft.
* **Six fields to start,** the ones Bagheera reads: `type`, `title`, `updated`, `expires` and `approved_by` on every page, and `repos` on service pages, so session start finds the page.
* The path is the page's ID and holds its domain; a feature entry is named after its ticket.
* **Grown by the pilot.** A field is added only when a pilot page needs it and something reads it, by a PR to the schema in the library repo. At the end of the pilot, the schema is fixed for the teams that adopt next.

### Consequences

* Good, because nothing is filled in that nobody reads, and the format check has little to get wrong.
* Good, because the schema other teams adopt is the one real pages needed.
* Bad, because later additions bring their own fields, and pages need updating then.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
