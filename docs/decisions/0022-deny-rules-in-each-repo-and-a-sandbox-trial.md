---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Block forbidden actions with deny rules committed in each repo; trial the sandbox in the pilot

## Context and problem statement

PRD R4: deploy commands, force-push, production credentials and edits to protected paths are blocked outright. Claude Code's deny rules match a command as Claude usually writes it, not the same program run by its full path, inside `sh -c` or from a script. Its sandbox enforces file and network limits at the operating-system level, but Docker doesn't run inside it, and it needs a list of allowed hosts. A plugin can't carry permission or sandbox settings, and a team must adopt with nothing from org admins. How are forbidden actions blocked, and where do the settings live?

## Considered options

* A. Deny rules in each repo's committed `.claude/settings.json`; the pilot team also trials the sandbox
* B. Deny rules in a file each developer adds to their own settings
* C. Managed settings from the claude.ai admin console, which developers can't change

## Decision outcome

Set by the owner: A.

* **One template.** `seeonee/bagheera` holds the settings template: deny rules for the four forbidden actions, bypass mode off, and a rule letting Claude read the local library copy without prompts. The architecture council edits the deny rules, and security approves changes to the template ([0025](0025-review-changes-to-bagheera-by-pr.md)).
* **Committed in each repo.** When a repo is seeded, its team commits the template as `.claude/settings.json`, merging it into any settings the repo already has, and may add deny rules of its own. It applies to everyone working in that repo; developers copy nothing.
* **What holds.** Deny rules win over allow rules from any settings file, so nobody's own settings can loosen them. Each repo's branch rule blocks force-pushing its main branch, and changes to the settings file arrive only through a reviewed PR.
* **The sandbox trial.** The pilot team's repos also turn the sandbox on: Claude Code won't start without it, credential folders are unreadable, listed hosts are reachable, production hosts are denied, and any other host prompts.
* **Red-teamed before the pilot.** On the test repo, the platform team tries to make Claude deploy, force-push, read production credentials and edit protected paths, directly, through scripts, by full path and inside `sh -c`, once with deny rules alone and once with the sandbox.
* **Decided at the end of the pilot:** the sandbox for everyone, for some repos, or deny rules only.

### Consequences

* Good, because developers set up nothing, and the rules are the same for everyone in a repo.
* Good, because the sandbox's real friction is measured on real work before other teams depend on it.
* Bad, because each repo holds a copy of the template; when the template changes, each team updates its repos.
* Bad, because a developer can still edit the file in their own checkout without committing it; the rules hold by agreement until managed settings.
* Bad, because until the sandbox decision, deploy commands and production credentials are blocked only in the forms Claude usually writes, so R4's "outright" isn't fully met.

## More information

Detail in [design/standards-and-forbidden-actions.md](../design/standards-and-forbidden-actions.md). Sources: Claude Code docs, [permissions](https://code.claude.com/docs/en/permissions) and [sandboxing](https://code.claude.com/docs/en/sandboxing).
