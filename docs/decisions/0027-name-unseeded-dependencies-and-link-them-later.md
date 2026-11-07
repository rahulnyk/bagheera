---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Name dependencies that have no page yet, with their repo

## Context and problem statement

Repos are seeded one at a time ([0026](0026-seed-each-domain-just-before-its-teams-join.md)), so a seeded service often calls services that have no library page yet. How does its page refer to them?

## Considered options

* A. Name it in plain words with its full repo name, and link it once it has a page
* B. Also seed the services it calls, even other teams'
* C. Leave it out until it has a page

## Decision outcome

Chosen option: A.

* The page's "Depends on" section names the service in plain words with its full repo name, for example "ledger-svc (`seeonee/ledger-svc`): records each charge."
* Once that service has a page, the next change to the page links it.
* A third-party service, such as a payment provider, is named in plain words only and never gets a page.

### Consequences

* Good, because it needs no new parts, and each team reviews only its own pages.
* Bad, because until the link is added, Claude sees only the dependency's name, not its interfaces.

## More information

Detail in [design/seeding.md](../design/seeding.md).
