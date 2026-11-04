---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Protect the library with GitHub owner review; keep stages soft

## Context and problem statement

Some library changes need a person: inferred service-page changes, decisions and standards ([0013](0013-merge-only-restated-approved-work-without-a-person.md)). Stage documents use a soft `/approved` comment ([0002](0002-soft-approval-by-comment.md)). The library is different: every session trusts it ([0008](0008-caution-and-provenance-for-the-knowledge-library.md)). How is approval given and enforced on library PRs?

## Considered options

* A. The same as stages: an `/approved` comment by the page's owners, enforced by process
* B. GitHub's normal review approval, required by branch rules on the library repo only, with owners generated from `domains.yaml`
* C. An `/approved` comment plus a custom required check that looks for it

## Decision outcome

Chosen option: B. Changes to standards, services and decisions need an owner's GitHub approval; feature entries, the index and the change log merge on the checks alone. The harness's GitHub App can open PRs but not approve them or change who the owners are. An owner's approval of a library PR is what verifies the inferred statements in it.

### Consequences

* Good, because nothing reaches the library's trusted pages without an owner, using a button reviewers already know.
* Good, because nothing custom has to be built; the owners file is generated from `domains.yaml`.
* Bad, because owners need write access to the library repo.
* To test early: branch rules that require owner approval on some paths and none on others (PRD Appendix A).

## More information

Detail in [design/knowledge-library.md, section 6](../design/knowledge-library.md#6-approving-library-prs).

Amended by [0086](0086-no-code-owners-approvers-recorded-and-checked-by-the-merger.md): no code owners in the pilot; approvers are recorded and checked by whoever merges.
