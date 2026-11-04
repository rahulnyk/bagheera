---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# The pilot runs Verify, the library update and the dashboard update on laptops; one GitHub App at rollout

## Context and problem statement

Three jobs work beyond one developer's session: Verify ([0037](0037-verify-as-an-org-ruleset-workflow.md)), the curation run with Close (design doc 1, section 6), and the dashboard sync ([0049](0049-github-is-the-record-dashboard-holds-derived-status.md)). In CI they need the Claude admin to set up a Console workspace and identity rules ([0061](0061-ci-signs-in-to-claude-with-workload-identity-federation.md)), and the GitHub org owners to create a ruleset and repo property and install a GitHub App. That is a lot of support from other teams before a pilot of a handful of engineers can start. Where do these jobs run in the pilot, and what gives them GitHub access at rollout?

## Considered options

* A. CI from day one, as designed
* B. Laptops in the pilot, from commands run with people's own access; CI at rollout
* C. GitHub's own models in CI instead of Claude Code

## Decision outcome

Set by the owner: B, with one GitHub App at rollout.

* **Verify:** the developer runs `/bagheera:verify` before marking a phase PR ready. A fresh Claude that never sees the author's conversation reviews it, posts the findings and marks the commit `bagheera/verify` passed or failed. Each pilot repo's branch rules require the mark.
* **The library update:** a manager runs `/bagheera:update-library` on demand, every few days. It does what the curation run does: library updates from merged PRs, Close and bookkeeping, and the weekly check when one is due.
* **The dashboard update:** a manager runs `/bagheera:update-dashboard` on demand, every few days. It refreshes status, assignees and the criteria results table, and posts the weekly summary when one is due.
* **Own access only.** Each command runs with the person's own Claude seat and GitHub sign-in. The pilot has no CI workflow, Console setup or GitHub App.
* **At rollout,** CI runs the same commands as designed in [0037](0037-verify-as-an-org-ruleset-workflow.md) and [0061](0061-ci-signs-in-to-claude-with-workload-identity-federation.md). One GitHub App gives them their GitHub access, including the dashboard sync. How Verify gets GitHub access without the App's key being readable from product repos is settled in the rollout plan.

### Consequences

* Good, because the pilot needs nothing from the Claude admin or the GitHub org owners; each pilot repo's admins set one branch rule.
* Good, because nothing is thrown away: the commands are what CI runs later.
* Bad, because the dashboard and the library are only as fresh as the last run. Approval requests still reach people straight away, since the stage commands request reviewers themselves.
* Bad, because PRD G1's pilot target, service pages updated within a day of a merge, needs the library update at least daily.
* Bad, because Verify's cost shows in each developer's own usage, so the PRD's "CI cost per merged PR" can't be measured in the pilot.
* Bad, because PRD P7's "in CI (stages 6–8)" holds only from rollout.

## More information

Option C was set aside because GitHub's models aren't Claude Code: we would build our own review and curation programs, CI would review differently from laptops, and PRD §8 names Claude Code and claude-code-action for CI. Detail in [design/distribution-security-and-cost.md, section 3](../design/distribution-security-and-cost.md#3-where-the-jobs-run).

Amended by [0063](0063-one-github-app-key-shared-with-onboarded-repos.md): the App's key is one organisation secret shared with the onboarded repos, so the rollout plan has nothing left to settle there.
