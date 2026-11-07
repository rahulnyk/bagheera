# Design docs

The six design docs describe Bagheera's full design: how each area works, in enough detail to build it. [The plan](../plan.md) says what's built first and when each further part arrives. The [PRD](../prd.md) is the source of truth for what Bagheera must do, and the [decisions](../decisions/) record why it's designed this way.

**Where things stand (2026-11-04).** All six docs are approved and current with the decisions of 2026-11-04. The plan is a draft for the owner's review.

## The docs

| # | Doc | What it settles | PRD requirements |
| --- | --- | --- | --- |
| 1 | [`knowledge-library.md`](knowledge-library.md) | The library's layout and page format, shelf life, what it learns and when, how it's kept current, approved and checked, and how sessions read it | K1–K7, K9, K10 |
| 2 | [`seeding.md`](seeding.md) | How the seeding skill drafts the first service pages, and how they're reviewed | K8 |
| 3 | [`developer-workflow.md`](developer-workflow.md) | The stage commands, what each writes, approval, open questions, build, demo, and what runs where | P1–P7, C1–C4, C6 |
| 4 | [`standards-and-review.md`](standards-and-review.md) | Rules within a standard, writing standards, revealing them, the PR review, forbidden actions, and the standards check | R1–R6 |
| 5 | [`status-and-dashboard.md`](status-and-dashboard.md) | Status, the dashboard, notifications, the weekly summary and the Jira link | V1–V7, C5 |
| 6 | [`distribution-security-and-cost.md`](distribution-security-and-cost.md) | Packaging and versions, settings, where jobs run, CI sign-in, spend, adoption and cost, and changing Bagheera | D1–D4, P4, V6 |

## Moving parts

A block-level diagram of these parts and how they connect is in [components.drawio](../components.drawio); open it in the draw.io app or at app.diagrams.net.

### What the platform team runs

| Part | What it is | When |
| --- | --- | --- |
| `seeonee/bagheera` | The plugin's source and the settings files; at rollout, the Verify and dashboard workflows | The first system |
| The plugin | The commands, `seed-service-page`, Baloo and a read-only code reader, the session-start hook, scripts and templates, and the Atlassian connector | The first system, with six commands |
| The library repo, Council Rock | Pages, `index.md`, and the format check with its workflow | The first system |
| Verify workflow | On every PR in onboarded repos, through one org ruleset | Rollout |
| Scheduled jobs | The library update and the dashboard sync, in CI | Rollout |
| GitHub App | CI's GitHub access | Rollout |
| GitHub Project | The dashboard | When the dashboard is added |

No servers and nothing self-hosted.

### What we need from other teams

| Who | What | When |
| --- | --- | --- |
| The org's Claude Code plugin marketplace, under the org's policies (assumed) | Publish each Bagheera version; let the pilot engineers install it, and everyone at rollout | Each version |
| Architecture council | Seed and approve the standards; edit the deny list | Before the pilot, then ongoing |
| Security | Approve changes to the settings files | When they change |
| Each team | Review its seeded pages; once the criteria table is added, save JUnit results in its CI | Once per repo |
| Approvers | Approve stage documents and library PRs | Ongoing |
| Managers | Hold teams to approvals and QA review; grant exceptions | Ongoing |
| Pilot repos' admins | One branch rule requiring the `bagheera/verify` mark | When Verify is added |
| GitHub org owners | Install the GitHub App; define the `bagheera` repo property; create the org ruleset; create the `bagheera-weekly` readers team | Once, at rollout |
| Claude admin | Paste the reviewed managed settings; for CI, a Console workspace, a service account and two identity rules | At rollout, and when the settings file changes |

Jira needs no admin work: developers' own access is enough (confirmed by the owner, 2026-10-30).

### Outside services

| Service | Used for |
| --- | --- |
| GitHub (Team or Enterprise plan, for org rulesets on private repos) | Code, PRs, approvals, CI, the dashboard |
| Jira and Confluence | Ticket text for Intake and its link comment; the pages standards are seeded from |
| Anthropic | Claude: Team or Enterprise seats on laptops; at rollout, a Console workspace, billed per use, for CI |

## Open items

| Item | Settled |
| --- | --- |
| The sandbox for everyone, for some repos, or deny rules only ([0045](../decisions/0045-deny-rules-now-sandbox-trial-in-pilot.md)) | At the pilot exit |
| Spend caps ([0068](../decisions/0068-no-spend-caps-until-after-the-pilot.md)) | At the pilot exit |
| Slack reminders ([0053](../decisions/0053-github-notifications-now-slack-later.md)) | At the pilot exit |
| A workflow for managers to renew expired pages ([0071](../decisions/0071-newest-wins-and-approvers-recorded-on-each-page.md)) | When pages expire unchanged and nobody renews them |
| How demo feedback becomes more work ([0075](../decisions/0075-one-pr-per-ticket.md)) | When Demo is added |
| Claude plan and model access (PRD Q7, Q8) | The owner, before week 1 |
| Jungle Book names ([0010](../decisions/0010-naming.md)) | The naming register, as components come up |

## PRD changes

Changes made to the PRD to match the owner's decisions, all on 2026-11-04.

| PRD | Change | Because of |
| --- | --- | --- |
| G5 | Adoption and cost by repository | [0066](../decisions/0066-report-job-spend-by-repository.md) |
| §5 stages 3 and 5 | Plan: 2–6 phases, built one at a time. Build: tests first, then code, in the ticket's one PR | [0075](../decisions/0075-one-pr-per-ticket.md) |
| §5 stage 7; C6; P7 | A demo script from the criteria, demoed live or recorded, accepted on the ticket's PR. The same commands on laptops and in CI | [0035](../decisions/0035-demo-run-by-the-developer.md), [0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) |
| §5 stage 8 | Close's library changes approved by a person | [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md) |
| §6 priorities | The pilot starts with the first system and adds the rest one at a time | [0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md) |
| K1 | An index; git history as the change log | [0078](../decisions/0078-git-history-is-the-change-log-library-update-keeps-the-index.md) |
| K4 | Agents never merge; every library change needs a person's approval, recorded on the page and the PR | [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [0086](../decisions/0086-no-code-owners-approvers-recorded-and-checked-by-the-merger.md) |
| K6 | Checked when made; shelf life; the newer page wins | [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [0071](../decisions/0071-newest-wins-and-approvers-recorded-on-each-page.md) |
| R5 | The architecture's standards check | [0046](../decisions/0046-standards-check-in-the-architecture.md) |
| C3 | Unless the developer overrides on purpose, recorded on the PR | [0032](../decisions/0032-stage-check-stops-and-records-overrides.md), [0073](../decisions/0073-keep-the-override-in-the-first-system.md) |
| V6, V7 | Adoption and cost by repository | [0066](../decisions/0066-report-job-spend-by-repository.md) |
| §7 | Two pilot targets on at least 10 tickets; efficiency measured after the pilot | [0081](../decisions/0081-the-pilot-proves-it-works-end-to-end.md) |
| §8 | Spec Kit adapted into the plugin; our own seeding skill on DeepWiki-Open's method; the plugin through the org's marketplace, assumed to exist; the pilot on at least 10 tickets | [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md), [0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md), [0087](../decisions/0087-publish-to-the-orgs-plugin-marketplace.md), [0081](../decisions/0081-the-pilot-proves-it-works-end-to-end.md) |
| §9 | The first system, the pilot, additions one at a time, then org rollout | [0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md), [0081](../decisions/0081-the-pilot-proves-it-works-end-to-end.md), [0083](../decisions/0083-seed-only-the-repos-tickets-land-in.md) |
| §10 | Answered questions removed (Q2–Q6); Q8 and Q9 reworded | [0001](../decisions/0001-github-access-for-pms-qa-and-leadership.md), [0005](../decisions/0005-view-only-dashboard.md), [0006](../decisions/0006-jira-link-only.md), [0007](../decisions/0007-one-knowledge-library.md), [0011](../decisions/0011-stage-environment-for-demos.md), [0081](../decisions/0081-the-pilot-proves-it-works-end-to-end.md) |
| Appendix B | The seeding pass is our own skill | [0082](../decisions/0082-seeding-skill-borrows-deepwiki-opens-method.md) |

## Naming register

Themed names are proposed to the owner first ([0010](../decisions/0010-naming.md)). Commands, files, workflows and skills keep plain names.

| Component | Plain name | Themed name | Status |
| --- | --- | --- | --- |
| The product | Delivery harness | Bagheera | Approved (PRD) |
| The plugin, which prefixes every command | Delivery harness plugin | `bagheera` (commands are `/bagheera:<stage>`) | Approved ([0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md)) |
| The knowledge library | Knowledge library | Council Rock | Approved ([0023](../decisions/0023-name-council-rock-and-baloo.md)) |
| The library writer | Knowledge agent | Baloo | Approved ([0023](../decisions/0023-name-council-rock-and-baloo.md)) |
| The dashboard | Bagheera dashboard | None, kept plain | Approved ([0056](../decisions/0056-name-the-dashboard-plainly.md)) |
