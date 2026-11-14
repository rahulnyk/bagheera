---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Standards are numbered rules owned by the architecture council, drafted with one command

## Context and problem statement

Who owns the org's standards, how are rules named, and how are they written?

## Decision outcome

Chosen by the owner.

* Standards are org-level `standard` pages. The architecture council owns them and approves every change; it can hand a topic to a specialist team.
* Each standard's rules share a short prefix, assigned by the council, and are numbered `sec-1`, `sec-2` and so on. IDs are lower case, so they can't be mistaken for Jira keys. A number is never reused; a withdrawn rule stays on the page, marked retired.
* Every rule can be checked from a diff. Nothing a linter already enforces is restated.
* `/bagheera:standards seed <Confluence links>` drafts standards from the pages given, and reports what it left out. `add` and `update` change rules. It opens a PR only when asked.
* Plan and Build open the standards that apply.

### Consequences

* Good, because every finding can name the rule it breaks.
* Bad, because the first standards need the council's time before the pilot.
