---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Reveal the library incrementally, as a graph

## Context and problem statement

[0021](0021-load-a-compact-starter-set-at-session-start.md) loaded a starter set at session start, including an index of every accepted decision in the domain. The owner pointed out that this loads more than a ticket needs: the library is a graph of knowledge, each ticket needs only a small part of it, and that part should be revealed step by step. PRD K7 still requires the relevant service page at session start, and R2 requires the relevant standards when Claude designs or writes code.

## Considered options

* A. A starter set within a budget ([0021](0021-load-a-compact-starter-set-at-session-start.md))
* B. Incremental reveal: load only the service page and the feature's status line at start; each stage reveals what it must have; Claude explores further one step at a time
* C. Nothing up front; everything on demand

## Decision outcome

Chosen option: B. Header references are the graph's edges. Stage commands reveal the part of the graph their stage needs (standards at Architecture and Build, satisfying R2). The knowledge skill shows one-line summaries of a page's neighbours before opening any page. Each reveal is capped.

### Consequences

* Good, because sessions carry only what the work needs, and the cost grows with the work, not with the size of the library.
* Good, because the library's structure (typed headers, fixed shapes, short pages) is what makes incremental reveal work.
* Bad, because decisions and features need a `services` field, so the graph connects them to services (added to the page format).
* Open: how standards are revealed when someone codes outside the stage commands (design doc 4).

## More information

Detail in [design/knowledge-library.md, section 9](../design/knowledge-library.md#9-how-sessions-read-it).
