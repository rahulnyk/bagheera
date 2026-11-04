---
status: superseded by [0074](0074-any-change-cancels-an-approval.md)
date: 2026-10-30
deciders: RN (owner)
---
# Wording fixes keep an approval

## Context and problem statement

An approval covers a document as it was when given; any later change cancels it ([0032](0032-stage-check-stops-and-records-overrides.md)). The owner decided that fixing a typo should not cancel an approval. How does the harness tell a wording fix from a real change?

## Considered options

* A. A size threshold: small diffs keep the approval
* B. A change marked as a wording fix keeps the approval: the stage commands mark their own wording-only edits, people mark theirs with a commit trailer, and every such fix stays visible on the PR

## Decision outcome

Chosen option: B. Option A was set aside because a tiny edit can change the meaning, for example adding "not".

* A wording fix corrects spelling, grammar or formatting and changes no meaning.
* When a stage command edits an approved document, Claude decides whether the edit is wording only, and if so marks the commit with the trailer `Bagheera-Change: wording`. A person editing by hand adds the same trailer.
* The stage check keeps an approval when every later change to that document carries the trailer. Any other change cancels it, as before.
* The marked commits are listed on the PR, so approvers can see them and object; objections are handled by people, in keeping with soft enforcement.

### Consequences

* Good, because approvers aren't chased again for typo fixes.
* Bad, because the mark is trusted: a real change marked as wording keeps an approval it shouldn't. It stays visible on the PR, and managers deal with misuse.

## More information

Detail in [design/developer-workflow.md, section 4](../design/developer-workflow.md#4-approval). Amends [0032](0032-stage-check-stops-and-records-overrides.md).
