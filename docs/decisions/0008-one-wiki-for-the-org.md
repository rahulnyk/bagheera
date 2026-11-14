---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# One wiki for the org, linked into a graph

## Context and problem statement

Does the wiki live in one repo for the org, or beside the code in each repo?

## Considered options

* A. A wiki in each code repo
* B. One wiki for the org

## Decision outcome

Set by the owner: B. The wiki's job is to distil the org's knowledge so the codebase can be understood as a whole, including how services depend on each other. A PM drafting requirements for a new feature needs that whole picture, which one wiki per service can't give. The wiki is to the harness what Confluence is to people: all the knowledge in one place, interlinked into a graph.

* **One repo,** `seeonee/wiki`. Every page lives at `<type>/<domain>/<page>.md`: services, features, decisions and standards. A team picks its domain when it joins; org-wide pages use `org`.
* **Links make the graph.** Services link the services they depend on; features link the services they touched and their decisions; decisions link their feature.
* **Specs and plans stay in the code repo** ([0007](0007-every-document-is-a-typed-page-in-git.md)). The wiki's feature page distils them and links to them.
* **Read everywhere.** Spec drafting reads the whole wiki. Every Claude Code session starts with its index and the repo's service page.
* **The code wins.** When a page is wrong, `/bagheera:update-wiki` fixes it in the ticket's wiki PR.
* How a ticket's knowledge reaches the wiki is [0020](0020-developers-update-the-wiki-before-merging-enforced-by-a-check.md).

### Consequences

* Good, because PMs and sessions see across services, not just within one.
* Good, because knowledge from every team lands in one place.
* Bad, because a ticket's wiki changes live in a second PR, beside its code PR.
* Bad, because every team needs write access to the wiki.
