---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The format check lives in the library repo

## Context and problem statement

The library's format check runs on every library PR, including owners' own edits, and in `/bagheera:update-library` before it opens a PR ([0070](0070-knowledge-expires-and-every-change-is-checked-once.md)). Design doc 1 keeps the checker and page schemas in `seeonee/bagheera`, and checks links with lychee. A workflow in the library repo can't read another private repo with its own GitHub token. Where does the check live?

## Considered options

* A. In the library repo, with the page schemas; no lychee
* B. In `seeonee/bagheera`, fetched by the library's workflow with a stored token
* C. A copy in each

## Decision outcome

Chosen by the owner: A.

* The checker script and the page schemas live in the library repo, under `checks/`, changed only by the platform team.
* The library's workflow runs it on every PR with no secrets, and the branch rule requires it.
* `/bagheera:update-library` runs the same script from its local copy of the library.
* The script checks that links between pages resolve; lychee isn't used.

### Consequences

* Good, because there's one copy, no stored key, and nothing to install.
* Good, because the schemas sit next to the pages they describe.
* Bad, because links out of the library aren't checked.

## More information

Changes design doc 1, section 4's "the schemas live with the harness's checks, not in the library". Detail in [plan.md, section 1](../plan.md#the-knowledge-library).
