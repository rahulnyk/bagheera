---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# One knowledge library per team

## Context and problem statement

A team must be able to adopt Bagheera alone ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)). PRD K1 asks for one git repo of standards, service pages, decisions and feature entries. Sharing one repo across teams means agreeing who owns it, who merges and how it's laid out. Where does a team's library live?

## Considered options

* A. One library repo per team, named in the team's settings file
* B. One shared repo that every team joins, in domain folders
* C. One per team, plus read access to other teams' libraries

## Decision outcome

Chosen by the owner: A.

* **Each team creates its own library repo,** such as `seeonee/payments-library`, and names it in its settings file ([0045](0045-deny-rules-now-sandbox-trial-in-pilot.md)). The session-start hook and the commands read and write that repo.
* **The team owns everything in it:** service pages, decisions, feature entries, and its standards, seeded from the org's Confluence pages ([0084](0084-one-standards-command-seeds-adds-and-updates.md)).
* **Other teams' services** appear by name and repo only ([0027](0027-name-unseeded-dependencies-and-link-them-later.md)).
* **An org-wide library,** with org standards owned centrally, is part of the [org-adoption proposal](../proposals/org-adoption.md).

### Consequences

* Good, because a team needs nothing from other teams to start.
* Good, because each library is small, with clear owners.
* Bad, because knowledge doesn't cross teams: a session sees only its own team's pages.
* Bad, because teams may write the same org standard twice, from the same Confluence pages.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
