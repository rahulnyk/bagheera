# Design docs

The [architecture](../architecture.md) says what the parts are. These docs say how each area works, in enough detail to build it. We write them one at a time, and each one in small steps:

1. I bring one question, with 2–3 options and a recommendation.
2. You decide.
3. I write that section, record any real decision as an ADR in [decisions/](../decisions/), and commit.

A doc is done when you approve it. Then we start the next. After all six, the phased plan (`docs/plan.md`) follows.

**Where things stand (2026-11-04).** All six docs are approved. They describe the full design, the target we grow towards. The first system to build is much smaller: the delivery path and a simple knowledge library ([0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md); library rules in [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md) and [0071](../decisions/0071-newest-wins-and-approvers-recorded-on-each-page.md)). The rest is added one part at a time, when the pilot shows it's needed, in [`docs/plan.md`](../plan.md) (draft, with simplification questions in progress). The walkthrough decks and the [diagram](../components.drawio) show the docs as first approved, before the later simplifications; where they differ, the docs and decisions win. Decisions from 0069 on also win over the docs' text where they differ; each doc's "Changes since approval" section lists them.

## The docs, in order

| # | Doc | What it settles | PRD requirements | Status |
| --- | --- | --- | --- | --- |
| 1 | [`knowledge-library.md`](knowledge-library.md) ([walkthrough deck](knowledge-library-walkthrough.pptx)) | How the knowledge library is structured, where each piece of knowledge comes from, how it is curated, checked and corrected, and how sessions read it | K1–K7, K9, K10 | **Approved**; simplified by the owner on 2026-11-04 ([0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md), [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [0071](../decisions/0071-newest-wins-and-approvers-recorded-on-each-page.md)) |
| 2 | [`seeding.md`](seeding.md) | How a Claude Code skill drafts the first service pages and how those drafts are reviewed | K8 | **Approved**; simplified by the owner on 2026-11-04 ([0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), [0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md)) |
| 3 | [`developer-workflow.md`](developer-workflow.md) ([walkthrough deck](developer-workflow-walkthrough.pptx)) | The stage commands, how Spec Kit is carried, what each stage writes, how approval works, the build and demo steps | P1–P7, C1–C4, C6 | **Approved**; amended with the owner's answers on 2026-10-30 ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)–[0040](../decisions/0040-build-phases-one-at-a-time.md)); simplified by the owner on 2026-11-04 ([0072](../decisions/0072-named-pm-and-tech-lead-approve.md), [0074](../decisions/0074-any-change-cancels-an-approval.md)–[0076](../decisions/0076-criteria-ids-in-test-names-results-in-the-pr.md)) |
| 4 | [`standards-and-review.md`](standards-and-review.md) ([walkthrough deck](standards-and-review-walkthrough.pptx)) | Standards format and ownership, which standards each session gets, the PR review, blocking forbidden actions | R1–R6 | **Approved**; your answers of 2026-10-30 folded in ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md), [0048](../decisions/0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md)) |
| 5 | [`status-and-dashboard.md`](status-and-dashboard.md) | Where status data lives, the status model, the dashboard, notifications, the weekly summary, the Jira link | V1–V7, C5 | **Approved** |
| 6 | [`distribution-security-and-cost.md`](distribution-security-and-cost.md) | How the plugin reaches every machine, where the jobs run, CI credentials, spend, adoption and cost reporting, changing the harness safely | D1–D4, P4, V6 | **Approved** |

**Why this order.** The knowledge library is the heart of the design and the first rollout phase (PRD §9, Foundations), and the other docs refer to it. Seeding follows because it fills the library. The developer workflow comes next because standards, status and the dashboard all depend on what each stage writes. Distribution comes last because it depends on the Claude plan, which isn't known yet.

## Steps planned for each doc

These are the questions I expect to bring, in order. They will change as we learn.

**1. Knowledge library**
1. Provenance and trust: how each statement records its source, and which sources are trusted. **Done:** [0012](../decisions/0012-record-provenance-per-statement.md)
2. Merge rules under caution: what, if anything, may enter the library without a person. **Done:** [0013](../decisions/0013-merge-only-restated-approved-work-without-a-person.md)
3. Structure: folders, page types, and a layout that can split later ([0007](../decisions/0007-one-knowledge-library.md)). **Done:** [0014](../decisions/0014-lay-out-the-library-by-type-then-domain.md)
4. Page format: required fields, the Open Knowledge Format shape. **Done:** [0015](../decisions/0015-typed-page-headers.md). **Added by the owner:** brevity without losing context. **Done:** [0017](../decisions/0017-brief-pages-with-fixed-shapes-and-length-limits.md)
5. What each stage adds, and when feature entries are written. **Done:** [0016](../decisions/0016-learn-decisions-when-approved-features-at-close.md)
6. The curation run: trigger, inputs, what the knowledge agent may touch, how changes are grouped, how a reviewer's approval is recorded on a library PR. **Done:** [0018](../decisions/0018-protect-the-library-with-owner-review.md)
7. Checks: format, links, allowed paths, sources, injected instructions. **Done:** [0019](../decisions/0019-screen-for-planted-instructions-twice.md)
8. Corrections: failed checks, rejected proposals, contradictions, stale pages, the 90-day human verification, who looks after them. **Done:** [0020](../decisions/0020-send-findings-to-owners-as-library-prs.md)
9. How sessions read it: incremental reveal of a small part of the library's graph. **Done:** [0022](../decisions/0022-reveal-the-library-incrementally-as-a-graph.md), which replaces 0021

**2. Seeding**
1. What runs the seeding pass. **Done:** [0024](../decisions/0024-seed-service-pages-with-a-claude-code-skill.md), a Claude Code skill, which replaces DeepWiki-Open ([0009](../decisions/0009-seed-service-pages-with-deepwiki-open.md))
2. What the skill reads and writes: where it looks, how it treats text in the repo, large repos. **Done:** [0025](../decisions/0025-seeded-pages-may-cite-repo-docs-code-wins.md), code first, the repo's docs allowed
3. How drafts are reviewed and verified, without swamping owners with many PRs at once. **Done:** [0026](../decisions/0026-seed-each-domain-just-before-its-teams-join.md), each domain seeded just before its teams join, with review as an onboarding task
4. A per-repo runbook: who runs it, links to services that don't have a page yet, usage limits. **Done:** [0027](../decisions/0027-name-unseeded-dependencies-and-link-them-later.md), name them now and link them once they have a page

**3. Developer workflow**
1. How Spec Kit's content is copied into the plugin and kept up to date (copying was decided in [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)). **Done:** [0028](../decisions/0028-adapt-spec-kit-once-and-review-it-quarterly.md), adapt once and review every quarter
2. Command names ([0003](../decisions/0003-stage-names-from-the-prd.md) and the naming register). **Done:** [0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md), `/bagheera:<stage>` and `/bagheera`
3. What each stage writes, and the feature folder layout. **Done:** [0030](../decisions/0030-one-planning-pr-per-feature.md), one planning PR per feature with conflict-free branch rules, and [0031](../decisions/0031-one-ticket-one-repo.md), one ticket per repo (set by the owner)
4. Approval: the `/approved` comment, the stage check, and recorded overrides ([0002](../decisions/0002-soft-approval-by-comment.md)). **Done:** [0032](../decisions/0032-stage-check-stops-and-records-overrides.md), the command stops; deliberate overrides are recorded
5. Open questions and how answers reach the next stage. **Done:** [0033](../decisions/0033-answer-open-questions-in-pr-comments.md), plain PR comments, one finalising run (set by the owner)
6. Build: tests first, one PR per phase, criteria IDs as test names. **Done:** [0034](../decisions/0034-build-tests-first-and-show-criteria-results.md), tests first by the command; coverage checked and results shown by CI
7. Demo on the stage environment ([0011](../decisions/0011-stage-environment-for-demos.md)). **Done:** [0035](../decisions/0035-demo-run-by-the-developer.md), run by the developer off the repo; the PM approves on the planning PR (set by the owner)
8. Another iteration after demo feedback (added by the owner). **Done:** [0036](../decisions/0036-revise-with-one-command-minor-or-new-ticket.md), `/bagheera:revise`; PM approval optional for minor revisions; major changes become a new ticket
9. What runs locally and what runs in CI. **Done:** [0037](../decisions/0037-verify-as-an-org-ruleset-workflow.md), Verify as an org ruleset workflow; results table and Close on existing scheduled jobs

**4. Standards and review**

Doc 1 already set the standard page's header, shape, layout and owner approval ([0015](../decisions/0015-typed-page-headers.md), [0017](../decisions/0017-brief-pages-with-fixed-shapes-and-length-limits.md)).
1. Rules within a standard: IDs, and whether blocking is set per page or per rule (R1, R6). **Done:** [0041](../decisions/0041-blocking-per-page-numbered-rules.md), per page; rules numbered `sec-3`
2. Writing the first standards (Foundations). **Done:** [0042](../decisions/0042-draft-first-standards-from-owners-documents.md), Claude drafts from the owners' documents; owners edit and approve
3. Revealing the standards relevant to the work, including when someone codes outside the stage commands ([0022](../decisions/0022-reveal-the-library-incrementally-as-a-graph.md)). **Done:** [0043](../decisions/0043-reveal-standards-on-first-edit.md), a hook reveals them on the first edit of each kind of file
4. The PR review: what it checks, how findings are shown and reported (R3, R6). **Done:** [0044](../decisions/0044-blocking-findings-fail-verify-tech-leads-dismiss.md), blocking findings fail Verify; a tech lead can dismiss with a reason
5. Forbidden actions: deny rules, and whether a guard hook is needed (R4). **Done:** [0045](../decisions/0045-deny-rules-now-sandbox-trial-in-pilot.md), deny rules and GitHub's rules now; the sandbox trialled in the pilot
6. Project principles generated from the standards (R5). **Done:** [0046](../decisions/0046-standards-check-in-the-architecture.md), a standards check in the architecture, filled from the current standards

**5. Status and dashboard**
1. Where status data lives ([0005](../decisions/0005-view-only-dashboard.md)). **Done:** [0049](../decisions/0049-github-is-the-record-dashboard-holds-derived-status.md), GitHub is the record; the dashboard holds the derived status
2. The status model: stages, states, who it's waiting on, recorded overrides. **Done:** [0050](../decisions/0050-named-people-per-story-team-fallback.md), seven statuses, markers for managers, named people with team fallback
3. What the dashboard is built on. **Done:** [0051](../decisions/0051-dashboard-on-an-org-github-project.md), an org-level GitHub Project
4. Views and filters. **Done:** [0052](../decisions/0052-five-shared-dashboard-views.md), five shared views, "Waiting on me" first
5. Notifications. **Done:** [0053](../decisions/0053-github-notifications-now-slack-later.md), GitHub's own notifications for now; Slack later (set by the owner)
6. The weekly summary. **Done:** [0054](../decisions/0054-weekly-summary-as-a-github-issue.md), a weekly GitHub issue in `seeonee/bagheera`, mentioning a readers team and the owners named in it
7. The Jira link ([0006](../decisions/0006-jira-link-only.md)). **Done:** [0055](../decisions/0055-intake-comments-the-link-on-the-jira-ticket.md), Intake comments the planning PR's link on the ticket
8. The dashboard's name (naming register). **Done:** [0056](../decisions/0056-name-the-dashboard-plainly.md), "Bagheera dashboard", kept plain

**6. Distribution, security and cost**
1. Plugin packaging and the marketplace. **Done:** [0057](../decisions/0057-release-the-plugin-from-a-release-branch.md), one marketplace in `seeonee/bagheera`, released from a `release` branch
2. How settings reach laptops (depends on the Claude plan). **Done:** [0058](../decisions/0058-managed-settings-from-the-admin-console-pilot-installs-by-hand.md), the claude.ai admin console after the pilot; pilot engineers install the plugin by hand (set by the owner); [0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md), the pilot's deny rules and sandbox in each engineer's own settings (set by the owner); [0060](../decisions/0060-lock-plugins-and-mcp-servers-leave-own-files.md), at rollout, plugins only from our marketplace and MCP servers only from an approved list
3. Where the jobs run, and CI credentials. **Done:** [0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md), in the pilot Verify, the library update and the dashboard update run on laptops from commands; at rollout, CI with one GitHub App (set by the owner); [0063](../decisions/0063-one-github-app-key-shared-with-onboarded-repos.md), its key one org secret shared with onboarded repos (set by the owner). [0061](../decisions/0061-ci-signs-in-to-claude-with-workload-identity-federation.md), from rollout, each CI run's GitHub identity exchanged for short-lived Claude access, only for our workflows
4. Spend limits and reporting. **Done:** [0068](../decisions/0068-no-spend-caps-until-after-the-pilot.md), no caps for now; every run's cost is recorded, and caps are decided after the pilot (set by the owner; replaces [0065](../decisions/0065-cap-ci-spend-per-job-and-per-month.md))
5. Adoption and cost by team. **Done:** [0066](../decisions/0066-report-job-spend-by-repository.md), by repository instead: each run records its cost, and the weekly summary adds them up (set by the owner)
6. How changes to the harness are reviewed and released. **Done:** [0067](../decisions/0067-review-changes-by-pr-with-code-owners.md), PRs with code owners, tried with `--plugin-dir` before each release

## Moving parts and dependencies

Kept up to date with every doc, so the total stays visible (the owner's request, 2026-10-29, [0037](../decisions/0037-verify-as-an-org-ruleset-workflow.md)). A block-level diagram of these parts and how they connect is in [components.drawio](../components.drawio).

### What the platform team runs

| Part | What it is | Where designed |
| --- | --- | --- |
| `seeonee/bagheera` repo | The marketplace, the plugin, the workflows and their scripts; releases from its `release` branch | Docs 1–3; doc 6, section 1 |
| The plugin | `/bagheera`, the eight stage commands and `/bagheera:revise`; the knowledge skill; `seed-service-page`; `draft-standard`; Baloo and a read-only code reader (subagents); the session-start and first-edit hooks; the feature and stage-check scripts | Docs 1–3 |
| The library repo (Council Rock) | Pages, `domains.yaml`, `prefixes.yaml`, the library checker, lychee | Docs 1 and 4 |
| Verify workflow | On every PR in onboarded repos, through one org ruleset | Doc 3, section 9; doc 4 |
| Curation run | Hourly: library updates from merged PRs, Close, bookkeeping. Weekly: the library check | Doc 1, sections 6 and 8; doc 3, section 9 |
| Dashboard sync | Every 15 minutes: feature status, the criteria results table, and re-running Verify after a dismissal. Weekly: the summary issue | Doc 3, sections 6 and 9; doc 4, section 4; doc 5 |
| GitHub App | Opens library PRs, posts comments, writes the dashboard and the weekly summary issue | Doc 1, section 6; doc 5 |
| GitHub Project | The dashboard: story, phase and revision rows; fields written only by the sync job | Doc 5, section 3 |

In all: 2 repos, 1 plugin, 3 workflows, 1 GitHub App and 1 GitHub Project. No servers and nothing self-hosted.

**In the pilot** ([0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md), [0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md)): the first system only, which is the plugin and the two repos, with the library's format check as its one workflow. The library update runs from a command on a laptop, with the runner's own access. Verify, the dashboard, the GitHub App and the CI workflows are additions, each brought in when the pilot shows it's needed ([`docs/plan.md`](../plan.md)).

### What we need from other teams

| Who | What | How often | Where designed |
| --- | --- | --- | --- |
| GitHub org owners (the company's org managers; confirmed by the owner, 2026-10-30) | Install the harness's GitHub App; give read access to `seeonee/bagheera`, to pilot engineers first and everyone at rollout; define the `bagheera` repo property, settable by the platform team; create the org ruleset for Verify, which also blocks force-push and needs owner review on protected paths; create PM, tech lead and QA teams where missing, and the `bagheera-weekly` readers team for the weekly summary | Once, in Foundations (about an hour) | Docs 1, 3, 5 and 6 |
| Claude admin (an owner of the company's Claude org) | After the pilot, paste the reviewed managed settings into the admin console: the plugin for everyone, with the deny rules. For CI, before rollout: a Console workspace, a service account and the two identity federation rules | Once, and when the settings file changes; never per release | Docs 4 and 6 |
| Each team | JUnit test results from its CI; review its seeded service pages | Once per repo, at onboarding | Docs 2 and 3 |
| Approvers and page owners | Approve stage documents; review library PRs | Ongoing, as part of their normal work | Docs 1 and 3 |
| Architecture council (principal and senior staff) | Own the first standards, the prefix register and the deny list; edit them as needed | Foundations, then ongoing | Doc 4 ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md)) |
| Managers | Hold teams to approvals, QA review and tests first, using the dashboard; grant exceptions | Ongoing | Doc 3 ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)) |
| Security | Code owner review of what runs on laptops in `seeonee/bagheera`: hooks, scripts, MCP settings and the settings files | When those change | Doc 6, section 6 ([0067](../decisions/0067-review-changes-by-pr-with-code-owners.md)) |

**For the pilot,** much less is needed ([0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md), [0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md)): nothing from the Claude admin, the GitHub org owners or the pilot repos' admins. The platform team, which administers `seeonee/bagheera` and the library repo, gives the pilot team access ([`docs/plan.md`](../plan.md), section 2). The `bagheera/verify` branch rule and the dashboard's org project arrive with Verify and the dashboard.

After setup, onboarding a repo needs nobody outside the platform team and the joining team. Jira needs no admin work: developers' Jira access is already available (confirmed by the owner, 2026-10-30).

### Outside services

| Service | Used for |
| --- | --- |
| GitHub (Team or Enterprise plan, for org rulesets on private repos) | Code, PRs, approvals, CI, the dashboard |
| Jira | Ticket text for Intake; one link comment per ticket, posted by Intake |
| Anthropic | Claude: Team or Enterprise seats on laptops; a Console workspace, billed per use, for CI |

### Removed along the way

DeepWiki-Open and its Docker setup ([0024](../decisions/0024-seed-service-pages-with-a-claude-code-skill.md)); a PR per planning stage ([0030](../decisions/0030-one-planning-pr-per-feature.md)); CI access to stage, test accounts in CI and recording storage ([0035](../decisions/0035-demo-run-by-the-developer.md)); per-repo workflow files ([0037](../decisions/0037-verify-as-an-org-ruleset-workflow.md)).

## Open items to resolve inside the docs

| Item | Why it's open | Resolved in |
| --- | --- | --- |
| Soft approvals versus PRD C3 "refuses to start" | Decided: the stage command stops without approval; deliberate overrides are recorded and shown ([0032](../decisions/0032-stage-check-stops-and-records-overrides.md)) | Resolved; C3 wording to update |
| Carrying Spec Kit in the plugin versus the PRD's "Spec Kit with a preset and extension" | Decided: copy its content into the plugin ([0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)) | Resolved; PRD §8 wording to update |
| Jungle Book component names versus PRD §8, Appendix B and CLAUDE.md | [0010](../decisions/0010-naming.md) proposes about five themed names | Naming register, as components come up |
| Caution versus PRD K4 | Decided: only restated approved work merges without a person ([0013](../decisions/0013-merge-only-restated-approved-work-without-a-person.md)) | Resolved; K4 holds in a narrower form |
| Status data in the knowledge library versus PRD K1's page types | Decided: status is derived from GitHub and lives only in the dashboard ([0049](../decisions/0049-github-is-the-record-dashboard-holds-derived-status.md)) | Resolved |
| DeepWiki-Open needs an Anthropic API key via LiteLLM, not a Claude subscription | Decided: seed with a Claude Code skill instead, which works on a subscription or an API key ([0024](../decisions/0024-seed-service-pages-with-a-claude-code-skill.md)) | Resolved |
| The sandbox for everyone, for some repos, or deny rules only; until then R4 is only partly met for deploy commands and production credentials, and in the pilot the deny rules hold by agreement ([0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md)) | [0045](../decisions/0045-deny-rules-now-sandbox-trial-in-pilot.md) | `docs/plan.md`, as a pilot exit decision |
| PRD G1's "updated within a day" pilot target, against library updates run every few days in the pilot | Decided: the target is dropped from the PRD ([0064](../decisions/0064-drop-the-updated-within-a-day-target.md)) | Resolved; PRD updated |
| PRD §7's "CI cost per merged PR" in the pilot, where Verify runs on people's own seats | Decided: each Verify run records its cost, on laptops too ([0066](../decisions/0066-report-job-spend-by-repository.md)) | Resolved |
| Spend caps (PRD D4, "capped per job"): none until the pilot gives real costs | [0068](../decisions/0068-no-spend-caps-until-after-the-pilot.md) | `docs/plan.md`, as a pilot exit decision |
| A small workflow for managers to review expired library pages and renew them | Later, not now ([0071](../decisions/0071-newest-wins-and-approvers-recorded-on-each-page.md)) | After the pilot |
| Slack integration: GitHub's scheduled reminders, after the pilot | [0053](../decisions/0053-github-notifications-now-slack-later.md) | `docs/plan.md` |
| Each repo's CI must save JUnit test results for the criteria table | [0034](../decisions/0034-build-tests-first-and-show-criteria-results.md) | Resolved for now: not needed until the criteria results table is added ([0076](../decisions/0076-criteria-ids-in-test-names-results-in-the-pr.md)); then an onboarding step |
| Claude plan and model access (Q7, Q8) not confirmed | The design now assumes a Team or Enterprise plan, which the admin console needs ([0058](../decisions/0058-managed-settings-from-the-admin-console-pilot-installs-by-hand.md)); CI sign-in and team cost reporting also depend on it | Doc 6; owner to confirm |

## PRD wording to update

Agreed decisions that change the PRD's wording. The PRD is the owner's document, so these are proposals for the owner to apply or approve.

| PRD section | Current wording | Change | Because of |
| --- | --- | --- | --- |
| §8 Preferred building blocks | GitHub Spec Kit "customised through an org preset … and an org extension" | Spec Kit's templates and method, copied into the plugin as commands with the PRD's stage names | [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md) |
| §8 Preferred building blocks; §9 Foundations; Appendix B | "DeepWiki-Open, run once per repo, to draft the first service pages (K8)"; "first service pages drafted by DeepWiki"; "the one-off DeepWiki run … keep them" | A seeding skill run in Claude Code, once per repo, with no DeepWiki-Open fallback; Q8's "DeepWiki setup" removed. **Applied** to §8, §9 and Q8 on the owner's decision, 2026-11-04; Appendix B, a record of the first attempt, is left as written | [0024](../decisions/0024-seed-service-pages-with-a-claude-code-skill.md), [0077](../decisions/0077-short-seeding-report-no-deepwiki-fallback.md) |
| C3 | "A stage refuses to start until its artifact is approved by the designated role" | Add: "unless the developer overrides on purpose; every override is recorded and shown" | [0032](../decisions/0032-stage-check-stops-and-records-overrides.md) |
| §5 stage 7; C6; P7 | Demo output "Short script and recording of the acceptance criteria running"; C6 "so the PM can accept without a meeting"; P7 "in CI (stages 6–8)" | A short demo script from the criteria; the developer demos it live or shares a recording, as they choose; the PM approves on the planning PR. P7: Verify and Close in CI, Demo run by the developer; in the pilot, Verify and Close run on laptops | [0035](../decisions/0035-demo-run-by-the-developer.md), [0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) |
| R5 | "The feature pipeline's templates and project principles are generated from the standards" | The architecture template has a standards check section, filled from the current standards on each run; no separate principles file | [0046](../decisions/0046-standards-check-in-the-architecture.md) |
| §7, G1 | "Pilot service pages updated within a day of a merge that changes them: ≥ 90%" | Removed. **Applied** to the PRD on the owner's instruction, 2026-11-04 | [0064](../decisions/0064-drop-the-updated-within-a-day-target.md) |
| V6 | "Adoption and cost per team: active users, sessions, spend, PRs with AI assistance" | Adoption and cost per repository, from each job's recorded cost and GitHub; per-person figures in Claude's own dashboards | [0066](../decisions/0066-report-job-spend-by-repository.md) |
| K4 | "Agents may merge service and feature page changes after automated checks" | Agents never merge; every library change needs its owners' approval | [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md), replacing [0013](../decisions/0013-merge-only-restated-approved-work-without-a-person.md) |
| K6 | "A scheduled check reports broken links, contradictions, stale service pages (code changed, page didn't) and orphan pages" | Every library change is checked for format and links when it's made; pages expire after their shelf life instead of being re-checked | [0070](../decisions/0070-knowledge-expires-and-every-change-is-checked-once.md) |
| §9 Rollout | Foundations, then the pilot of stages 1–4, then 5–8, then org rollout | First the simplest system, the delivery path and the knowledge library, piloted end to end; then additions one at a time, as the pilot shows the need; then org rollout | [0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md) |
| §5 stages 3 and 5 | Plan: "2–6 phases, each shippable on its own behind a flag"; Build: "Tests first, then code, one PR per phase" | Plan: "2–6 phases, built one at a time"; Build: "Tests first, then code, in the ticket's one PR". **Applied** to the PRD on the owner's decision, 2026-11-04 | [0075](../decisions/0075-one-pr-per-ticket.md) |

## Naming register

Themed names are proposed to the owner first ([0010](../decisions/0010-naming.md)). Commands, files, workflows and skills keep plain names.

| Component | Plain name | Themed name | Status |
| --- | --- | --- | --- |
| The product | Delivery harness | Bagheera | Approved (PRD) |
| The plugin, which prefixes every command | Delivery harness plugin | `bagheera` (commands are `/bagheera:<stage>`) | Approved ([0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md)) |
| The knowledge library | Knowledge library | Council Rock | Approved ([0023](../decisions/0023-name-council-rock-and-baloo.md)) |
| The knowledge agent | Knowledge agent | Baloo | Approved ([0023](../decisions/0023-name-council-rock-and-baloo.md)) |
| The dashboard | Bagheera dashboard | None, kept plain | Approved ([0056](../decisions/0056-name-the-dashboard-plainly.md)) |
