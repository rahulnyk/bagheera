---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Publish the plugin to the org's plugin marketplace; its owning team governs it

## Context and problem statement

[0057](0057-release-the-plugin-from-a-release-branch.md) has the platform team create a Claude Code plugin marketplace in `seeonee/bagheera` and release from its `release` branch. [0067](0067-review-changes-by-pr-with-code-owners.md) makes security review what runs on laptops, and [0060](0060-lock-plugins-and-mcp-servers-leave-own-files.md) locks plugins and MCP servers to approved ones. In an org, a plugin marketplace is normally run and governed by an owning team. Whose job is publishing and governing Bagheera's plugin?

## Considered options

* A. Publish to the org's plugin marketplace; its owning team governs publishing
* B. Our own marketplace in `seeonee/bagheera`, as in 0057

## Decision outcome

Set by the owner: A. Once the plugin is built, publishing it and the governance around it are outside Bagheera's scope.

* **Ours:** building the plugin, testing it with `claude --plugin-dir` on the test repo, and cutting each version by raising the version in `plugin.json`. Every change is still a PR to `main`, approved by the other platform engineer.
* **The marketplace's owning team's:** publishing each version, who can install it, the review it needs before publishing, including security's review of what runs on laptops (PRD D2), and the lock on other plugins and MCP servers (PRD D1).
* **Dropped from our design:** our own marketplace, the `release` branch, and our release and lock rules.
* **Still ours:** the deny rules file, which isn't part of the plugin ([0059](0059-pilot-settings-in-engineers-own-settings-file.md)).
* **To confirm** before week 1: the org's marketplace and its owning team, and how quickly they publish a fix during the pilot.

### Consequences

* Good, because the platform team builds the harness and nothing around its distribution.
* Good, because Bagheera is governed like every other plugin in the org.
* Bad, because fixes during the pilot reach engineers only as fast as the marketplace publishes them.

## More information

Replaces [0057](0057-release-the-plugin-from-a-release-branch.md). Amends [0060](0060-lock-plugins-and-mcp-servers-leave-own-files.md) and [0067](0067-review-changes-by-pr-with-code-owners.md), whose locks and laptop review move to the marketplace's owning team. Detail in [plan.md, section 1](../plan.md#install-and-the-deny-rules-file).
