---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# No code owners in the pilot; approvers are recorded and checked by whoever merges

## Context and problem statement

The design routes every library PR to its owners through a `CODEOWNERS` file and a branch rule that requires their approval ([0018](0018-protect-the-library-with-owner-review.md), [0070](0070-knowledge-expires-and-every-change-is-checked-once.md)). `seeonee/bagheera` uses one too, so security approves what runs on laptops ([0067](0067-review-changes-by-pr-with-code-owners.md)). Owners change all the time across the org, so these lists would always be out of date. Checking the design also showed gaps: two teams on one line means either can approve alone, `index.md` has no good owner, and a page can't name the person who approves its own library PR.

## Considered options

* A. Keep `CODEOWNERS` and fix the gaps
* B. No `CODEOWNERS`: record who approved, and check it as a process

## Decision outcome

Set by the owner: B. As long as the approvers' names are recorded, the tool doesn't need to know who they should be. Who approves is enforced at the process level.

* **No `CODEOWNERS`** in the library repo or in `seeonee/bagheera` during the pilot.
* **Branch rules with no lists of people:** changes go through a PR, need at least one approval, and, in the library, need the format check to pass.
* **Reviewers are requested by name.** `/bagheera:update-library` requests the ticket's tech lead and PM named in its spec ([0072](0072-named-pm-and-tech-lead-approve.md)); for merged PRs with no ticket, the person running it names a reviewer. Seeding and `/bagheera:standards` request whoever the person running them names.
* **The library repo's owners merge,** the platform team in the pilot, only once they see an approval from a fitting person: the tech lead for service pages and technical decisions, the PM for product decisions, the architecture council for standards.
* **Names are recorded.** A page's `approved_by` names the people whose `/approved` comments stand behind it, and its library PR by number ([0071](0071-newest-wins-and-approvers-recorded-on-each-page.md)). GitHub records who approved and who merged that PR.
* **In `seeonee/bagheera`,** the other platform engineer approves every change. A change to hooks, scripts, `.mcp.json` or settings also needs security's approval, which its author asks for and whoever merges checks (PRD D2).
* **Later, and minimal:** page ownership is planned separately, when the pilot or rollout shows it's needed.

### Consequences

* Good, because there's no list of owners to keep up to date.
* Good, because every approval is still on record, on the page and on the PR.
* Bad, because a wrong approver is caught only by whoever merges, not by GitHub.
* Bad, because the platform team merges every library PR during the pilot.

## More information

Amends [0018](0018-protect-the-library-with-owner-review.md), [0067](0067-review-changes-by-pr-with-code-owners.md), [0070](0070-knowledge-expires-and-every-change-is-checked-once.md) and [0071](0071-newest-wins-and-approvers-recorded-on-each-page.md) for the pilot. Detail in [plan.md, section 1](../plan.md#the-knowledge-library).
