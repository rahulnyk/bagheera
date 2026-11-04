---
status: superseded by [0022](0022-reveal-the-library-incrementally-as-a-graph.md)
date: 2026-10-28
deciders: RN (owner)
---
# Load a compact starter set at session start; read the rest on demand

## Context and problem statement

PRD K7 asks that every session can read the library and starts with the relevant service page in context. Everything loaded costs tokens in every session and competes for attention; anything not loaded must be looked up. How much should a session load up front?

## Considered options

* A. Everything relevant up front: service pages, all the domain's decisions, every standard that applies, related feature entries
* B. A compact starter set within a budget, with full pages read on demand through the knowledge skill
* C. Nothing up front; everything on demand

## Decision outcome

Chosen option: B. The starter set is the repo's service page, the standards that apply, where the current feature stands, summaries of services it depends on in other domains, and an index of the domain's and org's decisions. Its budget is about 10,000 tokens. Over budget, advisory standards go first, then other-domain summaries, then the decisions index.

### Consequences

* Good, because every session starts with what matters most, at a predictable cost.
* Good, because the brevity rules ([0017](0017-brief-pages-with-fixed-shapes-and-length-limits.md)) keep the starter set small.
* Bad, because Claude must choose to look up anything outside the starter set; the knowledge skill and the decisions index make that easy.

## More information

Detail in [design/knowledge-library.md, section 9](../design/knowledge-library.md#9-how-sessions-read-it).
