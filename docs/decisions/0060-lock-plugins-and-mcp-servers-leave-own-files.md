---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Lock plugins and MCP servers; leave people's own files alone

## Context and problem statement

PRD D1: engineers can't add unapproved extensions, and everyone runs the same version. From rollout, managed settings come from the claude.ai admin console ([0058](0058-managed-settings-from-the-admin-console-pilot-installs-by-hand.md)); in the pilot nothing can be locked. Extensions that run code reach a laptop as plugins and MCP servers. Hooks are commands an engineer or a repo sets up; skills, agents and CLAUDE.md files are text Claude reads. What do the settings lock?

## Considered options

* A. Lock plugins and MCP servers; leave people's own hooks, skills, agents and CLAUDE.md files alone
* B. A, plus Anthropic's official marketplace open to everyone
* C. A, plus hooks, skills and agents only from Bagheera

## Decision outcome

Chosen option: A.

* **Plugins** come only from our marketplace, `seeonee/bagheera` at `release`. The flags that load a plugin for one session are rejected, and plugins synced from claude.ai don't load in Claude Code.
* **MCP servers** come only from an approved list in the settings file, starting with the Jira connector.
* **Hooks, plain skills, agents and CLAUDE.md files** in repos and personal settings keep working as today.
* **An extra plugin** someone wants is reviewed and added to our marketplace, pinned to an exact commit, so everyone who installs it runs the same version.

### Consequences

* Good, because outside code reaches laptops only through reviewed, pinned entries.
* Good, because nothing changes for what teams already keep in their repos and personal settings.
* Bad, because the platform team reviews each extra plugin and MCP server people ask for.
* Bad, because personal and repo hooks stay unlocked; they are the engineer's or the repo's own commands, reviewed in their own repos.

## More information

Detail in [design/distribution-security-and-cost.md, section 2](../design/distribution-security-and-cost.md#2-how-settings-reach-laptops). Who approves changes to the lists is design doc 6, section 6.

Amended by [0067](0067-review-changes-by-pr-with-code-owners.md): the lock no longer rejects `--plugin-dir` and the other one-session flags, so platform engineers can try unreleased versions; the marketplace and MCP server locks stay.

Amended by [0087](0087-publish-to-the-orgs-plugin-marketplace.md): the plugin is published to the org's plugin marketplace, whose owning team governs publishing, its review and the lock on other plugins.
