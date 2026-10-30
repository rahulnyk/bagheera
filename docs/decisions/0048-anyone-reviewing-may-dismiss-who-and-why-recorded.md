---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# Whoever reviews the PR may dismiss a finding; who and why are always recorded

## Context and problem statement

[0044](0044-blocking-findings-fail-verify-tech-leads-dismiss.md) let a tech lead of the repo's domain, never the PR's author, dismiss a Verify finding with a reason. The owner doesn't want a hard rule on who may dismiss.

## Decision outcome

Set by the owner: managers or senior people reviewing the PR may dismiss findings, and the tool enforces no rule about who. Every dismissal must give a reason, and the tool records who dismissed it and why, on the PR's Verify check and in the weekly report by team and rule. In keeping with "managers enforce, the harness records", a dismissal by the PR's own author is accepted but highlighted in the weekly report, so managers can see it.

### Consequences

* Good, because the people best placed to judge a finding can dismiss it without waiting on a particular role.
* Good, because every dismissal is documented: who, why, which rule and which PR.
* Bad, because the tool no longer prevents self-dismissal; managers rely on the report.

## More information

Detail in [design/standards-and-review.md, section 4](../design/standards-and-review.md#4-the-pr-review). Amends [0044](0044-blocking-findings-fail-verify-tech-leads-dismiss.md).
