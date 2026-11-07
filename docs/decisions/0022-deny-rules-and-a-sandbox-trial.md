---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# Block forbidden actions with deny rules in each developer's settings; trial the sandbox in the pilot

## Context and problem statement

PRD R4: deploy commands, force-push, production credentials and edits to protected paths are blocked outright. Claude Code's deny rules match a command as Claude usually writes it, not the same program run by its full path, inside `sh -c` or from a script. Its sandbox enforces file and network limits at the operating-system level, but Docker doesn't run inside it, and it needs a list of allowed hosts. A plugin can't carry permission or sandbox settings, and a team must adopt with nothing from org admins. How are forbidden actions blocked, and where do the settings live?

## Considered options

* A. Deny rules in a settings file each developer adds to their own settings; the pilot team also trials the sandbox
* B. A, with the sandbox required for everyone from the start
* C. Managed settings from the claude.ai admin console, which developers can't change

## Decision outcome

Set by the owner: A.

* **One template.** `seeonee/bagheera` holds a settings template: deny rules for the four forbidden actions, bypass mode off, and a rule letting Claude read the local library copy without prompts. The platform team keeps it ([0025](0025-review-changes-to-bagheera-by-pr.md)).
* **One file per team.** Each team copies the template into its library repo, adds its library's name, and may add deny rules of its own. Each developer adds the team's file to `~/.claude/settings.json`.
* **What holds.** Deny rules win over allow rules from any settings file, so a repo's own settings can't loosen them. Each repo's branch rule blocks force-pushing its main branch.
* **The sandbox trial.** The pilot team also turns the sandbox on: Claude Code won't start without it, credential folders are unreadable, listed hosts are reachable, production hosts are denied, and any other host prompts.
* **Red-teamed before the pilot.** On the test repo, the platform team tries to make Claude deploy, force-push, read production credentials and edit protected paths, directly, through scripts, by full path and inside `sh -c`, once with deny rules alone and once with the sandbox.
* **Decided at the end of the pilot:** the sandbox for everyone, for some repos, or deny rules only.

### Consequences

* Good, because setup needs no admin rights and nothing from org admins.
* Good, because the sandbox's real friction is measured on real work before other teams depend on it.
* Bad, because the settings hold by agreement: a developer can change their own file.
* Bad, because until the sandbox decision, deploy commands and production credentials are blocked only in the forms Claude usually writes, so R4's "outright" isn't fully met.

## More information

Detail in [design/standards-and-forbidden-actions.md](../design/standards-and-forbidden-actions.md). Sources: Claude Code docs, [permissions](https://code.claude.com/docs/en/permissions) and [sandboxing](https://code.claude.com/docs/en/sandboxing).
