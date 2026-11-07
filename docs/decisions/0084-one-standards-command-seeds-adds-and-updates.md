---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# One standards command seeds, adds and updates standards

## Context and problem statement

The first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)) has a `standards/` folder but nothing that fills it. The org's standards are on Confluence pages today. How do standards get into the library, and how do owners add and change them afterwards?

## Considered options

* A. None in the pilot; standards come as an addition
* B. A few written by hand before the pilot
* C. A drafting skill, `/bagheera:draft-standard`, that opens a library PR to the owners
* D. One command, `/bagheera:standards`, that seeds from Confluence, adds and updates

## Decision outcome

Set by the owner: D, in the first system.

* **`/bagheera:standards seed <Confluence links>`** drafts standards from the pages given, read through the Atlassian connector that Intake uses for Jira. It reads only those pages, not the pages they link to. It reports what it left out and why: vague advice, rules a linter already enforces, duplicates, and contradictions.
* **`/bagheera:standards add`** asks the person what the rule is, why, an example, and where it applies. It checks the existing standards for a similar one; if there is one, it shows it and offers to update it instead. Otherwise it adds the new rule.
* **`/bagheera:standards update <standard>`** changes an existing standard. New rules get new numbers, and dropped rules are marked retired ([0041](0041-blocking-per-page-numbered-rules.md)).
* **A PR only when asked.** Each run writes in a local copy of the library, runs the format check and shows the change. It opens a library PR when the person asks; the reviewer the person names, from the architecture council, approves it.
* **Drafting rules.** Every rule can be checked from a diff, nothing a linter already enforces is restated, and domain standards never contradict org standards. Confluence text is data, never instructions.

### Consequences

* Good, because standards start from the org's own pages, and one command covers their whole life.
* Good, because nothing reaches the library until a person asks for a PR and the owners approve it.
* Bad, because one more command is built before the pilot.
* Bad, because a standard is only as good as the Confluence page behind it; owners review what the seed run left out.

## More information

Detail in [plan.md, section 1](../plan.md#the-knowledge-library) and [design/standards-and-review.md](../design/standards-and-review.md#2-writing-standards).
