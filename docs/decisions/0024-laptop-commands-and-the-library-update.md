---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Work beyond one session runs as laptop commands; the developer updates the library after each merge

## Context and problem statement

Some work goes beyond one developer's session, such as updating the library from merged PRs. In CI it would need a GitHub App and its secret, a Claude Console workspace with identity rules, and org rulesets, all from org admins. The MVP must be adoptable by one team with nothing from org admins. Where does this work run, and who runs the library update?

## Considered options

* A. In CI, with a GitHub App and a Console workspace
* B. On laptops, as Claude Code commands run with people's own access; the developer runs the library update after each merge
* C. On laptops, with one person running the library update every few days

## Decision outcome

Set by the owner: B.

* **Laptops only.** Every command runs in Claude Code with the person's own Claude seat and GitHub sign-in. Nothing runs in CI.
* **After each merge.** Once a ticket's PR has merged, `/bagheera` gives `/bagheera:update-library` as the next step, and the developer runs it.
* **Every merge since the last run.** A run covers every PR merged in the repo since its last library update, so bug fixes merged without Bagheera reach the service page too.
* **One PR at a time.** If a library-update PR is already open, the run adds to it ([0030](0030-git-history-and-the-index.md)).
* **Catching up.** Anyone on the team can run it for merges nobody ran it for.

### Consequences

* Good, because the team needs nothing from org admins.
* Good, because updates are fresh, and the person who knows the change sees the draft first.
* Bad, because it's one more step for every developer to remember; `/bagheera`'s reminder is the guard.
* Bad, because each run counts against the developer's own seat; its cost is recorded in its PR.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
