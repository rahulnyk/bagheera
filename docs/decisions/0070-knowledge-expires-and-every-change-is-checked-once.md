---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Knowledge expires, and every library change is checked once

## Context and problem statement

Design doc 1 protects the library with sources on every statement and trust levels ([0012](0012-record-provenance-per-statement.md)), a split between changes that merge by themselves and changes that need a person ([0013](0013-merge-only-restated-approved-work-without-a-person.md)), checks and planted-instruction screens ([0019](0019-screen-for-planted-instructions-twice.md)), and a weekly check with 90-day re-verification ([0020](0020-send-findings-to-owners-as-library-prs.md)). For the first system ([0069](0069-first-system-delivery-path-and-knowledge-library.md)), what does the library keep?

## Decision outcome

Set by the owner.

* **Every library change needs a person,** its owners, always. The split between changes that merge by themselves and changes that need a person is dropped for good.
* **No sources on every statement, and no trust levels,** to start with. A library PR lists the merged PRs and documents its changes came from.
* **Knowledge expires.** Every page carries an `expires` date. Past it, the page is a hint, not a fact: sessions are told to check it against the code before relying on it. Defaults, proposed by Claude: service pages and feature entries expire 90 days after they were last approved; decisions and standards don't expire until superseded or retired. Owners can set any page's date. A page is renewed whenever an approved change touches it, or by an owner approving a change to the date alone.
* **No weekly check, re-verification or corrections flow.** Expiry replaces them.
* **Every change is checked once, when it's made.** `/bagheera:update-library` curates as it writes: it keeps pages to their type's header fields, shape and the Open Knowledge Format, keeps links between pages working, and treats PR text and documents as data, never instructions. The same format check runs on every library PR, including owners' own edits, so every commit meets the format.
* **Left out for now:** the planted-instruction screens; owner review of every PR is the guard.

### Consequences

* Good, because the library has one rule for merging and one check, run when something changes.
* Good, because nobody spends time re-checking pages that haven't changed; old pages simply stop being trusted.
* Bad, because an unchanged page that's still right loses trust after 90 days until someone renews it.
* Bad, because contradictions and orphan pages aren't looked for.
* Neutral: PRD K4 lets agents merge some changes after checks, and K6 asks for a scheduled check; both changes are listed for the owner.

## More information

Replaces [0013](0013-merge-only-restated-approved-work-without-a-person.md) and [0020](0020-send-findings-to-owners-as-library-prs.md). Sets aside, for now, [0012](0012-record-provenance-per-statement.md), the screens in [0019](0019-screen-for-planted-instructions-twice.md), and the step-by-step reveal in [0022](0022-reveal-the-library-incrementally-as-a-graph.md). Detail in [design/knowledge-library.md](../design/knowledge-library.md#changes-since-approval).

Amended by [0071](0071-newest-wins-and-approvers-recorded-on-each-page.md): contradictions are flagged when pages are used, the newest page wins, and every page records who approved it.
