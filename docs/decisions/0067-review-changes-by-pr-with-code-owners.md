---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Review changes to Bagheera by PR, approved by the other platform engineer

## Context and problem statement

PRD D2: changes to Bagheera are reviewed like production code, with security owning the parts that run on laptops. The org's plugin marketplace reviews and publishes each version of the plugin ([0087](0087-publish-to-the-orgs-plugin-marketplace.md)). How are changes made and tried before a version is published?

## Considered options

* A. A PR to `main`, approved by the other platform engineer; tried with `--plugin-dir` on the test repo
* B. A, with `CODEOWNERS` making security approve the hooks, scripts and settings
* C. A, plus a CI workflow that tests each PR's plugin against a test repo

## Decision outcome

Chosen option: A.

* **Every change is a PR to `main`,** approved by the other platform engineer. `main` needs a PR and doesn't allow force-push.
* **Tried before each version.** A platform engineer loads `main` with `claude --plugin-dir plugins/bagheera` and runs the changed commands on the test repo.
* **A version** raises the version in `plugin.json`; the marketplace publishes it. Rolling back is a new version that reverts the change.
* **The settings file also needs security's approval,** which its author asks for and whoever merges checks.
* **No fast lane.** Urgent fixes take the same path.

### Consequences

* Good, because it is GitHub's own review, in the platform team's own repo, with no lists of owners to keep.
* Good, because trying a change takes one command on a platform engineer's machine.
* Bad, because with two platform engineers, a change waits when one is away.
* Bad, because security's approval of the settings file is checked by a person, not by GitHub.

## More information

Detail in [design/distribution-security-and-cost.md](../design/distribution-security-and-cost.md).
