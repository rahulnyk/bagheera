---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Screen library changes for planted instructions with a pattern script and a model pass

## Context and problem statement

PR descriptions, tickets and pasted documents are untrusted (PRD K5). If text planted in them reached a library page, every future session would load it. How do we screen library changes?

## Considered options

* A. A model screen only: a second Claude pass with no tools reads the page changes
* B. A pattern script only: fixed rules for text aimed at an AI, hidden content, outside links, and text copied word for word from untrusted inputs
* C. Both, on every library PR; any finding blocks the merge

## Decision outcome

Chosen option: C. Both screens run on every library PR, including PRs that would otherwise merge by themselves, and any finding blocks the merge. A test set of planted inputs is run before the pilot and whenever the agent's instructions or the screens change.

### Consequences

* Good, because each screen catches what the other misses.
* Good, because the word-for-word rule directly enforces that external text is never stored as a statement.
* Bad, because two screens mean more false alarms for owners to clear (section 8 of the design).

## More information

Detail in [design/knowledge-library.md, section 7](../design/knowledge-library.md#7-checks).
