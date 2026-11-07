---
status: accepted
date: 2026-11-03
deciders: RN (owner)
---
# Put the pilot's deny rules and sandbox in each engineer's own settings

## Context and problem statement

During the pilot, nothing is set in the admin console, and pilot engineers install the plugin by hand (0058). A plugin can't carry permission or sandbox settings. The pilot still needs the deny rules and the sandbox trial ([0045](0045-deny-rules-now-sandbox-trial-in-pilot.md)). Where do they go on each pilot Mac?

## Considered options

* A. A managed settings file copied once to the system folder on each pilot Mac, with admin rights
* B. Each engineer's own settings file, `~/.claude/settings.json`
* C. No deny rules or sandbox in the pilot; GitHub's server-side rules only

## Decision outcome

Set by the owner: B. Only a handful of trusted engineers take part, and they won't edit the settings.

* **One file to copy from.** `seeonee/bagheera` holds `settings/pilot-user-settings.json`, with the deny rules, bypass mode off and the sandbox settings. At setup each pilot engineer adds its contents to `~/.claude/settings.json`.
* **What still holds.** Deny rules beat allow rules from any settings file, so a repo's settings can't loosen them. The sandbox can still refuse to start when it's unavailable, and Claude still can't ask to run a command outside it.
* **What doesn't.** The engineer can change the file. The list of allowed hosts can't be locked outside managed settings, so an unlisted host prompts the engineer instead of being blocked.
* **Changes** to the deny list during the pilot are copied again by the pilot engineers, when the platform team asks.
* **At rollout,** the deny rules move into the console's settings file, where developers can't change them.

### Consequences

* Good, because setup needs no admin rights and nothing outside the engineer's own settings.
* Good, because the host prompts show which hosts the sandbox trial needs.
* Bad, because in the pilot the deny rules and sandbox hold by agreement, not by lock. GitHub's server-side rules still block force-push and protected paths.

## More information

Amends [0045](0045-deny-rules-now-sandbox-trial-in-pilot.md), whose pilot sandbox was to be required by managed settings. Detail in [design/distribution-security-and-cost.md, section 2](../design/distribution-security-and-cost.md#2-how-settings-reach-laptops).
