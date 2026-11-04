---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Review changes to Bagheera by PR, with code owners for what runs on laptops

## Context and problem statement

PRD D2: changes to Bagheera are reviewed like production code, with security owning the parts that run on laptops. Bagheera is released from `seeonee/bagheera`'s `release` branch ([0057](0057-release-the-plugin-from-a-release-branch.md)). At rollout the lock rejects `--plugin-dir` ([0060](0060-lock-plugins-and-mcp-servers-leave-own-files.md)), which is how platform engineers would try an unreleased version. How are changes reviewed and tried before release?

## Considered options

* A. Ordinary PR review with GitHub code owners; platform engineers try `main` with `--plugin-dir` before each release, and the lock stops blocking that flag
* B. A, plus an early-access group on a second marketplace from `main`
* C. Keep the flag blocked, and test each PR's plugin in a CI workflow against a test repo

## Decision outcome

Chosen option: A.

* **Every change is a PR to `main`,** approved by the other platform engineer.
* **Code owners:** security owns the hooks, scripts, MCP settings and settings files, alongside the platform team; the architecture council also owns the settings files, which hold the deny list ([0047](0047-architecture-council-owns-standards-prefixes-deny-list.md)). GitHub requires a code owner's approval for those paths on `main`.
* **Before a release,** a platform engineer loads `main` with `claude --plugin-dir` and tries the changes on a test repo.
* **A release** is the `main` to `release` PR with a higher version, approved by the other platform engineer. Rollback is a new release.
* **No fast lane:** urgent fixes take the same path.
* **The lock** no longer rejects `--plugin-dir`; the marketplace and MCP server locks stay.

### Consequences

* Good, because it is GitHub's own review and branch rules, set by the platform team in its own repo.
* Good, because trying a change takes one command on a platform engineer's machine.
* Bad, because anyone can load a plugin folder for one session at rollout. That is deliberate, rare, and no different from running their own code, so it is accepted.
* Bad, because with two platform engineers, a change waits when one is away.

## More information

Detail in [design/distribution-security-and-cost.md, section 6](../design/distribution-security-and-cost.md#6-reviewing-and-releasing-changes-to-bagheera). Amends [0060](0060-lock-plugins-and-mcp-servers-leave-own-files.md).

Amended by [0086](0086-no-code-owners-approvers-recorded-and-checked-by-the-merger.md): no code owners in the pilot; approvers are recorded and checked by whoever merges.

Amended by [0087](0087-publish-to-the-orgs-plugin-marketplace.md): the plugin is published to the org's plugin marketplace, whose owning team governs publishing, its review and the lock on other plugins.
