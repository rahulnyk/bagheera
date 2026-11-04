---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Give every page a typed header, checked against a schema

## Context and problem statement

PRD K2 asks for pages that follow a schema a machine can check. The harness needs header fields to pick pages for a session, route reviews, run checks and build views. K10 asks that the format stays portable, and the Open Knowledge Format is a candidate.

## Considered options

* A. A minimal header (type, ID, domain, owners, last verified); everything else in the text
* B. A typed header per page type, checked against a schema, compatible with the Open Knowledge Format
* C. The Open Knowledge Format's field set exactly, with our extras on top

## Decision outcome

Chosen option: B, with the field list in the design doc. Every page has a YAML header with a required `type`, so it stays compatible with the Open Knowledge Format without depending on a format that is still a draft.

### Consequences

* Good, because the harness can act on fields rather than parse prose.
* Good, because the schema check rejects missing or misspelt fields.
* Bad, because there are more fields to keep right; the knowledge agent fills them and the check enforces them.

## More information

Fields, statuses, an example and the `domains.yaml` format: [design/knowledge-library.md, section 4](../design/knowledge-library.md#2-pages).

Amended by [0085](0085-six-header-fields-the-pilot-grows-the-schema.md): in the first system, six header fields to start, grown by the pilot.
