---
status: accepted
date: 2026-11-03
deciders: RN (owner)
---
# Release the plugin from one marketplace, on a release branch

## Context and problem statement

PRD P4: the same commands on every machine, installed and updated centrally, with no developer setup. PRD D1: everyone runs the same version. The plugin is `bagheera` ([0029](0029-stage-commands-under-the-bagheera-plugin.md)) and lives in `seeonee/bagheera`. Claude Code installs plugins from a marketplace, a file in a repo that lists them; managed settings can add a marketplace on every machine, switch a plugin on so it can't be switched off, and turn on background updates. Claude Code fetches a new copy only when the plugin's version changes. How does each release reach everyone?

## Considered options

* A. One marketplace in `seeonee/bagheera`, released from a `release` branch; managed settings name the branch once
* B. The same, but managed settings name each release's exact tag
* C. claude.ai's organisation plugin sync (Team or Enterprise plans only)

## Decision outcome

Chosen option: A.

* **One marketplace, one plugin.** `seeonee/bagheera` holds a marketplace named `seeonee` that lists one plugin, `bagheera`, so its install name is `bagheera@seeonee`.
* **Releases.** Work merges to `main`. A release is a PR from `main` to `release` that raises the plugin's version; a check fails it otherwise.
* **Set once.** Managed settings add `seeonee/bagheera` at `release` with auto-update on, and switch on `bagheera@seeonee` for everyone.
* **Rollback** is a new release with the earlier content and a higher version.
* **CI** loads the same `release` branch.

### Consequences

* Good, because it is configuration only, and no one outside the platform team takes part in a release.
* Good, because it works whichever way managed settings reach laptops, so it doesn't wait on the Claude plan (PRD Q7).
* Good, because a release reaches everyone within about a session: Claude Code checks in the background soon after a session starts, and the new version loads at the next launch.
* Bad, because Claude Code fetches the repo with each developer's own git sign-in, so every engineer needs read access to `seeonee/bagheera` and a git credential stored on their machine.
* Bad, because for a session or two after a release, some people are still on the previous version.

## More information

Detail in [design/distribution-security-and-cost.md, section 1](../design/distribution-security-and-cost.md#1-packaging-and-releases). How changes are reviewed and tried before release is design doc 6, section 6.
