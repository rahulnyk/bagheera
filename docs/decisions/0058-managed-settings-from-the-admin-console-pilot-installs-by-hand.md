---
status: accepted
date: 2026-11-03
deciders: RN (owner)
---
# Managed settings from the claude.ai admin console; the pilot installs the plugin by hand

## Context and problem statement

Managed settings carry the plugin switch, the deny rules ([0045](0045-deny-rules-now-sandbox-trial-in-pilot.md)) and the lock on other plugins (PRD D1). They reach laptops through the claude.ai admin console, the company's device management, or both. The console applies one policy to everyone in the Claude org and can't target the pilot team alone. How do the settings reach laptops, and how does the pilot go first?

## Considered options

* A. The claude.ai admin console
* B. The company's device management (MDM), run by IT
* C. Both: the console for what changes, and a small fixed device profile that locks sign-in to the company's Claude org

## Decision outcome

Set by the owner: A, with a simpler path for the pilot.

* **After the pilot:** the settings live as one file in `seeonee/bagheera`, changed by PR. A Claude org owner pastes the merged file into the admin console, and Claude Code fetches it at startup and hourly.
* **During the pilot:** nothing is set in the console. Each pilot engineer, on a Mac, installs the plugin by hand from the same marketplace and `release` branch, with two commands, and turns on its auto-update.
* **At rollout,** the console's settings register the same marketplace and switch the plugin on for everyone, so pilot machines need nothing redone.

### Consequences

* Good, because no IT team is involved, and a settings change reaches everyone within about an hour.
* Good, because the pilot needs nothing from the Claude admin, and starts as soon as the plugin works.
* Bad, because the console needs a Team or Enterprise plan; the design assumes one (PRD Q7).
* Bad, because console settings are a client-side guard: they don't reach someone signed in with a personal account or an API key. GitHub's server-side rules still block force-push and protected paths.
* Bad, because in the pilot the plugin can be switched off and the deny rules and sandbox need a home outside the console; that is decided separately.

## More information

Detail in [design/distribution-security-and-cost.md, section 2](../design/distribution-security-and-cost.md#2-how-settings-reach-laptops).
