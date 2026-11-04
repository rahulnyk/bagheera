---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Report job spend by repository

## Context and problem statement

PRD V6 asks for adoption and cost per team. Claude's own analytics show usage and spend per person, not per team or repo. The jobs that cost the most, Verify and the library update, each run for a known repository. How is spend reported?

## Considered options

* A. Per team, from GitHub plus Claude's own dashboards, grouped by hand when asked
* B. Per team, automated through Claude's Enterprise Analytics API and a list matching people to teams
* C. Per repository: each job run records its own cost, and the weekly summary adds them up by repo

## Decision outcome

Set by the owner: C, the simplest.

* **Each run records its cost.** Claude Code reports a run's estimated cost when it finishes. Verify writes it next to its findings: on the PR in the pilot, and in its check run at rollout. The library update records its cost with its run.
* **The same way on laptops.** In the pilot, `/bagheera:verify` runs the review as a separate Claude Code process, as CI does, so its cost is known and it never sees the author's conversation.
* **The weekly summary adds them up:** Verify spend per repo, library-update spend, and Verify spend per merged PR, which is the PRD's "CI cost per merged PR".
* **Adoption by repo, from GitHub:** stories started and shipped, Bagheera PRs merged, and who used the stage commands that week.
* **Everything else stays in Claude's own dashboards,** linked from the weekly summary: active users, sessions and spend per person, and CI's workspace total.

### Consequences

* Good, because it needs no new tool, no key and no list of people to keep current.
* Good, because the PRD's cost per merged PR can now be measured in the pilot too.
* Bad, because developers' own sessions, the stage commands and building, aren't split by repo; that spend stays per person in Claude's dashboards.
* Neutral: the cost is Claude Code's estimate at API prices. For seat users it measures relative cost, not their bill.

## More information

Detail in [design/distribution-security-and-cost.md, section 5](../design/distribution-security-and-cost.md#5-adoption-and-cost-by-repository). Amends [0054](0054-weekly-summary-as-a-github-issue.md)'s cost section. PRD V6 says "per team"; the change to "per repository" is listed for the owner.
