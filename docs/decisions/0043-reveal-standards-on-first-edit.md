---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Reveal the relevant standards on the first edit of each kind of file, in every session

## Context and problem statement

PRD R2: the standards relevant to the language and service being changed are in Claude's context when it designs or writes code. The Architecture and Build stage commands reveal standards ([0022](0022-reveal-the-library-incrementally-as-a-graph.md)), but many sessions never use a stage command: a quick fix, a refactor. Claude Code hooks can add context when Claude edits a file, but that context arrives just after the edit, not before it. What gives Claude the standards in those sessions?

## Considered options

* A. Reveal on the first edit: a plugin hook adds the standards that apply the first time Claude edits each kind of file in a session
* B. Hold the first edit: the hook stops the first edit of each kind of file once, with the standards as the reason, and Claude tries again
* C. Load the standards for the repo's languages and service at session start

## Decision outcome

Chosen option: A. A plugin hook watches Claude's file edits. The first time in a session (or in a subagent) that Claude edits a file of a given language, or a deploy file, the hook adds the standards that apply: that language's, the service's, and the org-wide ones, each once, within design doc 1's limit of about 2,000 tokens per reveal. Claude re-checks the edit that triggered it and carries on. This one mechanism serves every session, so Build no longer reveals language standards itself. The Architecture stage still reveals blocking standards for design, and Verify catches anything that slips through.

### Consequences

* Good, because R2 holds in every session, stage command or not, with nothing for developers to do.
* Good, because it keeps incremental reveal: a session that never edits code loads no standards.
* Good, because one mechanism replaces Build's own reveal.
* Bad, because the first edit of each kind lands just before its standards arrive; Claude re-checks it straight after.
* Bad, because design discussions outside the stage commands get standards only when Claude looks them up through the knowledge skill.

## More information

Detail in [design/standards-and-review.md, section 3](../design/standards-and-review.md#3-revealing-the-relevant-standards). Settles the open point in [0022](0022-reveal-the-library-incrementally-as-a-graph.md).
