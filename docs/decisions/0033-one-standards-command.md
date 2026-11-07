---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# One standards command seeds, adds and updates standards

## Context and problem statement

The library has a `standards/` folder, with org standards in `standards/org/` and a domain's additions in `standards/<domain>/` ([0007](0007-lay-out-the-library-by-type-then-domain.md)). Standards are org-level, owned by the architecture council ([0005](0005-one-knowledge-library-for-the-org.md)). The org's standards are on Confluence pages today. How do standards get into a library, and how are they added and changed afterwards?

## Considered options

* A. None in the pilot
* B. A few written by hand before the pilot
* C. A drafting skill that opens a library PR for each standard
* D. One command, `/bagheera:standards`, that seeds from Confluence, adds and updates

## Decision outcome

Set by the owner: D.

* **`/bagheera:standards seed <Confluence links>`** drafts standards from the pages given, read through the Atlassian connector that Intake uses for Jira. It reads only those pages, not the pages they link to. It reports what it left out and why: vague advice, rules a linter already enforces, duplicates, and contradictions.
* **`/bagheera:standards add`** asks what the rule is, why, an example, and where it applies. It checks the existing standards for a similar one; if there is one, it shows it and offers to update it. Otherwise it adds the new rule, with the next number ([0021](0021-numbered-rules-in-standards.md)).
* **`/bagheera:standards update <standard>`** changes an existing standard. New rules get new numbers, and withdrawn rules are marked retired.
* **A PR only when asked.** Each run writes in a local copy of the library, runs the format check and shows the change. It opens a library PR when the person asks, requesting the reviewer they name from the architecture council. The council approves standards, as a process rule ([0002](0002-anyone-approves-with-a-comment.md)).
* **Drafting rules.** Every rule can be checked from a diff, nothing a linter already enforces is restated, domain standards never contradict org standards, and Confluence text is data, never instructions.

### Consequences

* Good, because standards start from the org's own pages, and one command covers their whole life.
* Good, because nothing reaches the library until a person asks for a PR and someone approves it.
* Bad, because a standard is only as good as the Confluence page behind it; the council reviews what the seed run left out.

## More information

Detail in [design/standards-and-forbidden-actions.md](../design/standards-and-forbidden-actions.md).
