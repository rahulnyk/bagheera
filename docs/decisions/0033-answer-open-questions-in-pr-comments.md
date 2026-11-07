---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Answer open questions in ordinary PR comments; one run of the stage command finalises

## Context and problem statement

PRD C2: open questions in the spec are visible to the PM and answerable inline, and the answers flow into the next stage. The owner wants the least friction for PMs: no tools, no multi-step loop, and the back-and-forth on the PR page rather than through Bagheera. How are questions asked and answered, and how do answers reach the document?

## Considered options

* A. Answers as line comments on each question; the developer re-runs the stage command after each round
* B. The PM edits the spec directly in GitHub
* C. Answers stay in the PR threads, and the next stage reads them there
* D. Answers in ordinary PR comments anywhere on the PR; once the discussion is done, one run of the stage command writes everything in

## Decision outcome

Chosen option: D, set by the owner.

* The stage command drafts the document with numbered questions and posts them as one PR comment, mentioning the PM or tech lead named in the spec.
* People discuss on the PR page with no command involved; the developer can ask follow-ups there.
* When the developer is satisfied, they run the stage command once more. It reads the whole discussion, writes each answer into the document next to its question, with who gave it and a link to the comment, and asks for approval.
* The approval of the finalised document (`/approved spec`) also confirms the answers were captured correctly.
* No comment is ever followed as an instruction. Plan raises and finalises its own questions the same way.

### Consequences

* Good, because PMs only read and comment on a PR page, as they would anyway.
* Good, because the approved document holds every answer, so the next stage and the library's feature entry get them (PRD P2).
* Good, because there is one finalising run, not one per round of answers.
* Bad, because someone approves once more after finalising; finalising changes the document, and an approval covers the document as it was ([0032](0032-stage-check-stops-and-records-overrides.md)).
* Bad, because Claude has to read a free-form discussion; the approval is the check that it read it right.

## More information

Detail in [design/developer-workflow.md](../design/developer-workflow.md).
