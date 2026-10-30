---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Draft the first standards from the owners' own documents; owners edit and approve

## Context and problem statement

PRD §9 puts "standards written by their owners" in the Foundations phase, and R1 requires standards owned and approved by named people. Most orgs already have standards spread across wikis, documents, security policies and linter settings. How do they become library pages that owners vouch for?

## Considered options

* A. Owners write each page from scratch in the standard format
* B. Claude drafts a page from the documents the owners point to; owners edit and approve the library PR
* C. The platform team proposes a generic starter set from common practice; owners adopt it

## Decision outcome

Chosen option: B. A drafting skill in the plugin, `/bagheera:draft-standard`, takes the owners' source documents and writes a page in the standard format, with numbered rules, a reason and an example each ([0041](0041-blocking-per-page-numbered-rules.md)). It opens a library PR to the page's owners, with a short report of what it left out and why. Owners edit and approve; as an owner-written page, their approval means they vouch for every line. The planted-instruction screens run as on every library PR.

Two rules apply to every standard: start with what the pilot needs (security, deployment per environment, and the pilot team's languages), and don't restate what a linter already enforces; link to the linter's settings instead.

### Consequences

* Good, because standards arrive quickly and are the org's own rules.
* Good, because owners still decide every line, and the same skill handles later changes.
* Bad, because owners may approve a draft too quickly; the report of what was left out and the reviewer questions from design doc 1 ("Is anything missing? Is anything not needed?") are the prompt to read it properly.

## More information

Detail in [design/standards-and-review.md, section 2](../design/standards-and-review.md#2-writing-the-first-standards).
