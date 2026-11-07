---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Developers run the library update after each merge

## Context and problem statement

[0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) has a manager run `/bagheera:update-library` on demand, every few days, during the pilot. That needs the manager to install and learn Claude Code, and to remember to run it. Who runs it in the first system ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md))?

## Considered options

* A. A platform engineer, every few days
* B. A manager, as in 0062
* C. The developer, after each merge

## Decision outcome

Chosen by the owner: C.

* **When:** once the ticket's PR has merged, `/bagheera` gives `/bagheera:update-library` as the next step.
* **What a run covers:** every PR merged in this repo since its last library update, so bug fixes merged without Bagheera reach the service page too.
* **One PR at a time:** if a library-update PR is already open, the run adds to it ([0078](0078-git-history-is-the-change-log-library-update-keeps-the-index.md)).
* **Access:** pilot engineers get write access to the library repo to push their branch; the branch rule still needs an approval.
* **The platform team** watches the library PRs, and runs it to catch up on merges nobody ran it for.

### Consequences

* Good, because updates are fresh, and the person who knows the change sees the draft first.
* Good, because nobody outside the team has to learn a new tool.
* Bad, because it's one more step for every developer to remember; the platform team sees merges with no library update.
* Bad, because the cost counts against each developer's own seat; each run still records it.

## More information

Amends [0062](0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) for the library update. Detail in [plan.md, section 1](../plan.md#the-knowledge-library).
