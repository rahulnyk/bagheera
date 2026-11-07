---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Publish the plugin through the org's plugin marketplace; its owning team governs it

## Context and problem statement

Claude Code installs plugins from a marketplace. In an org, the plugin marketplace is normally run and governed by an owning team. Whose job is publishing and governing Bagheera's plugin?

## Considered options

* A. Publish to the org's plugin marketplace; its owning team governs publishing
* B. Our own marketplace in `seeonee/bagheera`, released from a `release` branch

## Decision outcome

Set by the owner: A. Once the plugin is built, publishing it and the governance around it are outside Bagheera's scope.

* **Ours:** building the plugin, trying it with `claude --plugin-dir` on the test repo, and raising the version in `plugin.json` for each release. Every change is a PR to `main` ([0067](0067-review-changes-by-pr-with-code-owners.md)).
* **The marketplace's owning team's:** publishing each version, who can install it, the review it needs before publishing, including security's review of what runs on laptops (PRD D2), and any lock on other plugins and MCP servers (PRD D1).
* **Still ours:** the settings file, which isn't part of the plugin ([0045](0045-deny-rules-now-sandbox-trial-in-pilot.md)).
* **Assumed:** the org has a Claude Code plugin marketplace, governed by its internal policies.

### Consequences

* Good, because the platform team builds the harness and nothing around its distribution.
* Good, because Bagheera is governed like every other plugin in the org.
* Bad, because fixes during the pilot reach engineers only as fast as the marketplace publishes them.

## More information

Detail in [design/distribution-security-and-cost.md](../design/distribution-security-and-cost.md).
