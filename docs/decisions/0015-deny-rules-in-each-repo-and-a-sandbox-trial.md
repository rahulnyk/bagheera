---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Block forbidden actions with deny rules committed in each repo; trial the sandbox in the pilot

## Context and problem statement

Claude must not deploy, force-push, read production credentials or edit protected paths. Plugins can't carry permission settings, and managed settings need an org admin.

## Decision outcome

Set by the owner.

* A settings template in `seeonee/bagheera` holds deny rules for the four forbidden actions, with bypass mode off. Each repo commits it as `.claude/settings.json`, merged with its own settings, and may add rules.
* Each repo's default branch blocks force-push.
* The pilot team's repos turn on the sandbox: credential folders are unreadable, and production hosts are denied.
* The platform team red-teams it before the pilot. The pilot decides: the sandbox for everyone, for some repos, or deny rules only.
* The council edits the deny rules; security approves changes to the template.

### Consequences

* Good, because it needs nothing from org admins.
* Bad, because deny rules catch only the usual forms of a command. The sandbox closes the gap wherever it runs.
