---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# The format check lives in each library repo and runs on every library PR

## Context and problem statement

The library's format check runs on every library change, people's own edits included, and in the commands before they open a PR ([0070](0070-knowledge-expires-and-every-change-is-checked-once.md)). A workflow in the library repo can't read another private repo with its own GitHub token. Where does the check live, and what runs it?

## Considered options

* A. In each library repo, with its page schemas, run on every PR by a workflow there that the branch rule requires
* B. In `seeonee/bagheera`, fetched by each library's workflow with a stored token
* C. On laptops only: the commands run it before opening a PR

## Decision outcome

Chosen by the owner: A.

* The checker script and the page schemas live in the library repo, under `checks/`, copied from the library template in `seeonee/bagheera` when the team creates its library.
* A workflow in the library repo runs it on every PR with no secrets, and the branch rule requires it. It's the only workflow Bagheera uses.
* The commands run the same script from their local copy of the library before opening a PR.
* The script checks that links between pages resolve.

### Consequences

* Good, because there's one copy per library, no stored key, nothing to install and nothing from org admins.
* Good, because the schemas sit next to the pages they describe, and the team grows them by PR.
* Bad, because each library has its own copy of the checker; a team takes a newer one by copying it from the template.
* Bad, because links out of the library aren't checked.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
