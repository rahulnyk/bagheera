---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Block forbidden actions with deny rules and GitHub's rules now; trial the sandbox in the pilot

## Context and problem statement

PRD R4: deploy commands, force-push, production credentials and edits to protected paths are "blocked outright, not just discouraged". Claude Code's deny rules, set in managed settings, can't be overridden, but for shell commands they match the command as Claude usually writes it and, in Anthropic's words, aren't "a security boundary around the program". GitHub's server-side rules truly block force-push and changes to protected paths. Claude Code's sandbox enforces file and network limits at the operating-system level, but it doesn't run on native Windows, Docker doesn't run inside it, and it needs a maintained list of allowed hosts. The PRD assumes "there is no sandbox".

## Considered options

* A. Deny rules plus GitHub's rules
* B. A, plus the sandbox required everywhere from day one
* C. A now; trial the sandbox with the pilot team and decide before rollout
* (Set aside: our own guard hook that parses every command; code to maintain, still text-based.)

## Decision outcome

Chosen option: C.

* **Everyone:** managed deny rules for the four forbidden actions, and Claude Code's bypass-permissions mode switched off. On GitHub, the org ruleset blocks force-push and deletion of main branches, and changes to protected paths need their owners' review.
* **The pilot team:** also runs with the sandbox on, required by managed settings, with credential folders unreadable and only listed hosts reachable.
* **Before the pilot:** the platform team red-teams both setups in a test repo, trying to make Claude deploy, force-push, read production credentials and edit protected paths.
* **Pilot exit decision** (`docs/plan.md`): the sandbox for everyone, for some repos, or deny rules only.

### Consequences

* Good, because force-push and protected paths are truly blocked from day one, with no friction.
* Good, because the sandbox's real friction is measured on real work before 100 engineers depend on it.
* Bad, because until the decision, deploy commands and production credentials are blocked only in the forms Claude usually writes, outside the pilot team's sandboxed machines. R4's "outright" is not fully met for those two in the meantime.
* Neutral: the PRD's "there is no sandbox" assumption stands until the pilot decision.

## More information

Detail in [design/standards-and-review.md, section 5](../design/standards-and-review.md#5-forbidden-actions). Sources: Claude Code docs, [permissions](https://code.claude.com/docs/en/permissions) and [sandboxing](https://code.claude.com/docs/en/sandboxing).
