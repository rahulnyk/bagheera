---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Hard limits on the length of every page, command output and question

## Context and problem statement

Long documents don't get read, and documents nobody reads don't get approved or trusted.

## Decision outcome

Set by the owner: a heavy bias against verbosity is a must.

* **Pages:** each page type has a word cap, and the page check fails any page over it. Spec 400, plan 500, service 400, feature 300, decision 200, standard 500, headers excluded. Each section has its own limit too.
* **Command output:** at most 8 lines: pre-flight problems, what changed, the next step.
* **Questions:** only questions that block, at most 5 open at a time, one line each, the most blocking first.
* **Link, don't copy.** Reasoning, logs and discussion stay where they were written. No logs, transcripts, or code longer than 10 lines in a page.
* The same bias holds for Bagheera's own docs.

### Consequences

* Good, because a PM can read and approve a spec in minutes.
* Bad, because some features will strain the caps. The answer is a smaller ticket, or a cap tuned in the pilot, not a longer page.
