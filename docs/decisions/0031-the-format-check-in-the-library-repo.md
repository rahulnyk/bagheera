---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# The format check lives in the library repo and runs on every library PR

## Context and problem statement

The library's format check runs on every library change, people's own edits included, and in the commands before they open a PR ([0027](0027-approved-checked-and-expiring-knowledge.md)). A workflow in the library repo can't read another private repo with its own GitHub token. Where does the check live, and what runs it?

## Considered options

* A. In the library repo, with the page schemas, run on every PR by a workflow there that the branch rule requires
* B. In `seeonee/bagheera`, fetched by the library's workflow with a stored token
* C. On laptops only: the commands run it before opening a PR

## Decision outcome

Chosen by the owner: A.

* The checker script and the page schemas live in the library repo, under `checks/`. The platform team approves changes to them, as a process rule.
* A workflow in the library repo runs it on every PR with no secrets, and the branch rule requires it. It's the only workflow Bagheera uses.
* The commands run the same script from their local copy of the library before opening a PR.
* The script checks that links between pages resolve.

### Consequences

* Good, because there's one copy, no stored key, nothing to install and nothing from org admins.
* Good, because the schemas sit next to the pages they describe, and grow by PR.
* Bad, because links out of the library aren't checked.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
