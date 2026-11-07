---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# The architecture council owns the first standards, the prefix register and the deny list

## Context and problem statement

Standards need owners (PRD R1), every standard needs a unique prefix for its rule IDs ([0041](0041-blocking-per-page-numbered-rules.md)), and the deny list for forbidden actions needs someone to keep it ([0045](0045-deny-rules-now-sandbox-trial-in-pilot.md)). Who owns these, and where are prefixes written down?

## Decision outcome

Set by the owner:

* **The architecture council** (principal and senior staff) owns the first standards. It can hand a page to a specialist team later, for example security standards to the security team, by changing the page's `owners`.
* **Prefixes are assigned by the council and written down in the library**, in a register, `prefixes.yaml`, beside `domains.yaml`. The council owns the register; adding a prefix is a small library PR it approves. The library checker rejects a standard whose prefix isn't in the register, or points to another page. Spec Kit's constitution, the other place the owner suggested, was replaced by the standards check (0046), so the library is the home.
* **The deny list is seeded as part of the harness's scaffolding**, a default list in `seeonee/bagheera`, and the architecture council edits it. It reaches laptops through managed settings (design doc 6).

In `domains.yaml`, the `org` domain's owners are the architecture council's GitHub team.

### Consequences

* Good, because one senior group sets the first rules, the naming and the hard limits, consistently across the org.
* Good, because prefixes are documented where everyone looks, and the checker keeps the register and the pages in step.
* Bad, because the council is a bottleneck for new standards and prefixes until it hands pages on.

## More information

Detail in [design/standards-and-review.md, sections 1, 2 and 5](../design/standards-and-review.md#1-rules-within-a-standard). Amends [0041](0041-blocking-per-page-numbered-rules.md) and [0045](0045-deny-rules-now-sandbox-trial-in-pilot.md).

Amended by [0059](0059-pilot-settings-in-engineers-own-settings-file.md): during the pilot, the deny list reaches pilot laptops through each engineer's own settings, and moves into managed settings at rollout.
