---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# A standards check in the architecture, filled from the current standards

## Context and problem statement

PRD R5 (should have): the pipeline's templates and project principles are generated from the standards, "so specs and plans follow them by construction". Spec Kit does this with a per-repo principles file, its `constitution`, which design doc 3 dropped. What replaces it?

## Considered options

* A. A standards check section in the architecture template, filled on each run from the blocking standards that apply
* B. A generated principles file per repo, rebuilt whenever a standard changes
* C. Skip R5 for now; rely on revealing standards and on Verify

## Decision outcome

Chosen option: A. The architecture template has a short "Standards check" section. `/bagheera:architecture` fills it from the blocking standards revealed for the services involved: one line per rule that bears on the design, with how the design meets it or an explicit exception and its reason. The tech lead's approval of the architecture covers the section, exceptions included. An approved exception still appears as a finding in Verify; the tech lead dismisses it there, citing the approved architecture, so it is recorded and reaches the standard's owners in the weekly report.

### Consequences

* Good, because designs follow the standards by construction, and the tech lead sees compliance when approving.
* Good, because there's no new file and nothing to regenerate: the section is filled from the current standards on each run.
* Bad, because only blocking rules are listed, to keep the section short; advisory ones are followed but not shown.
* The PRD's wording for R5 ("templates and project principles are generated from the standards") should change: the template stays fixed, and its standards section is filled from them.

## More information

Detail in [design/standards-and-review.md, section 6](../design/standards-and-review.md#6-the-standards-check-in-the-architecture).

Amended by [0048](0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md): the person reviewing the PR, not necessarily the tech lead, dismisses the finding for an approved exception.
