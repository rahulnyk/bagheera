---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# PMs work in a portal they sign in to

## Context and problem statement

PMs aren't comfortable in GitHub, so reviewing and approving in pull requests didn't happen in practice. PMs and leadership also need to see where every feature stands. Where do people outside engineering take part?

## Considered options

* A. A portal: a web page they sign in to
* B. GitHub pull requests, with an account for each PM

## Decision outcome

Set by the owner: A.

* PMs draft specs, answer questions, approve and follow features in the portal. They need no git and no GitHub account.
* The portal shows every feature's stage and who it's waiting on, so nobody has to ask.
* The whole flow can be followed from the portal. How much of it runs there is [0003](0003-the-portal-is-one-small-web-app-on-the-agent-sdk.md).

### Consequences

* Good, because PMs work on a page made for them.
* Good, because the board is the status report.
* Bad, because we build and run a web app, which needs org admins once.
