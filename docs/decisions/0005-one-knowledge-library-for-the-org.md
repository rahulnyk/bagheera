---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# One knowledge library for the org, with org-level standards owned by the architecture council

## Context and problem statement

PRD K1 asks for one git repo of standards, service pages, decisions and feature entries. Standards are org-level by definition: if each team managed its own, there would be no standard. Teams must still be able to join one at a time ([0026](0026-the-mvp.md)). How is the library shared, and who owns the standards?

## Considered options

* A. One library repo for the org, with a folder per domain; standards owned by the architecture council
* B. One library per team, each owning its standards
* C. One library per team, plus one org library for the standards

## Decision outcome

Set by the owner: A.

* **One repo,** Council Rock, such as `seeonee/knowledge-library` (name to confirm, PRD Q1), laid out by page type, then domain ([0007](0007-lay-out-the-library-by-type-then-domain.md)). The plugin knows where it is, so nobody configures it.
* **Each team's pages go under its domain.** A team joins when the platform team, which runs the repo, gives it write access.
* **Standards are org-level.** Org standards live in `standards/org/`; a domain's additions live in `standards/<domain>/` and never contradict them. The architecture council owns both and, as a process rule, approves their changes ([0033](0033-one-standards-command.md)).

### Consequences

* Good, because there's one standard for everyone, one place to look, one format check and one index.
* Good, because a team's sessions see other teams' service pages and decisions.
* Bad, because every team shares one repo and its rules, and the architecture council is a bottleneck for standards.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
