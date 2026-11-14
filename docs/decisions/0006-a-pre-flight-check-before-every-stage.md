---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# A pre-flight check runs before every stage

## Context and problem statement

Branching, `main` versus `master`, missing connections, and re-running a stage after its spec or plan changed all produced corner cases that people had to work out by hand.

## Decision outcome

Set by the owner: a pre-flight check before every stage.

* One script, run first by every command, and by the portal before it drafts or commits.
* It checks:
  * the repo, and its default branch, read from GitHub and never assumed
  * the ticket's branch, clean and up to date
  * the local copy of the wiki
  * sign-ins and connectors
  * the stage's inputs
* It fixes what's safe: it creates or switches to the ticket's branch, pulls the branch and the wiki, and merges the default branch in. It stops on the rest, with one line per problem and its fix.
* Re-running a stage is the same command, and revises that stage's own file.

### Consequences

* Good, because problems show before work starts, with the fix.
* Good, because the branching rules live in one script instead of in people's heads.
* Bad, because every command waits a moment for GitHub.
