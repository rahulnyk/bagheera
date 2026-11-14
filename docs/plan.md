# Plan

_The MVP · draft for review, 2026-11-14 · [PRD](prd.md) · [Design](design.md)_

## 1. What we build

| Repo | Holds |
| --- | --- |
| `seeonee/bagheera` | The plugin, the portal, and the settings template |
| `seeonee/wiki` | The org's wiki: services, features, decisions and standards |
| `seeonee/bagheera-test` | A small service, with a Jira test project and a Confluence test page, for the end-to-end check |

### What we need from others

| Who | What | When |
| --- | --- | --- |
| Org admins | The portal's host, SSO client, GitHub App, Jira service account and model access (PRD Q2, Q3) | Asked in week 1 |
| The org's plugin marketplace | Publish each version | Each version |
| The architecture council | Seed and approve the first standards; edit the deny rules | Before the pilot |
| Security | Review the settings template and the portal's access | When they change |

## 2. Order

Two platform engineers on two tracks, 6–8 weeks to the pilot.

1. **Week 1, both.** Create the three repos; the wiki's default branch accepts changes only by PR. Ask the org for the portal's setup, which is the long pole, so it starts first. Publish an empty plugin to learn the marketplace's process.
2. **Weeks 1–5, in parallel.** Each step is tested before the next one starts.
   * **Plugin:** page templates and the page check → pre-flight → `/bagheera:spec` → `/bagheera:plan` → `/bagheera:build` → `/bagheera:update-wiki` → `/bagheera` → the session-start hook → `/bagheera:seed`, tried early on a real pilot repo → `/bagheera:standards`.
   * **Portal:** sign-in and the GitHub App → the board → the feature page → drafting on the Agent SDK → answers and *Approve* → the wiki check and merging wiki PRs → Jira comments.
3. **Weeks 5–6, both.** The end-to-end check and the red-team test; the guides; version 1.0.
4. **Week 6 or 7.** The council seeds the first standards, and the pilot team adopts Bagheera.

Pre-flight and the page check come first because every command uses them. The board comes before drafting because it proves the GitHub App and status work.

## 3. The pilot

One team of 6–8 engineers with its PM and tech lead, on at least 10 real tickets ([0019](decisions/0019-the-pilot-proves-it-works.md)).

* **Setup:** choose the team and tickets (PRD Q4). Seed the repos the tickets land in, the week before. Run a 30-minute walkthrough: the portal for PMs, the five commands for developers.
* **Targets:** at least 10 tickets, each with its spec drafted in the portal and approved by its PM before Build, then built and merged, with its wiki PR merged too. Counted from the PRs.
* **Noted weekly, not targets:** rounds of questions before approval; conflicts with the code found while drafting; specs changed after approval, and why; pre-flight stops, by kind; time from ticket to approved spec; run costs; deny-rule hits and sandbox friction.
* **Exit questions:** Were the targets met? Could the team adopt it from the guide alone? Which additions do the signals call for? Sandbox for everyone, or not? Which page types and caps does the pilot leave us with?

## 4. Adopting it

* **Once per team:** choose its domain; the platform team gives its engineers, PM and tech lead write access to the wiki.
* **Once per repo:** commit the settings template as `.claude/settings.json`; add the repo to the GitHub App; on the default branch, block force-push and require the `bagheera/wiki` check from the portal's app; run `/bagheera:seed` and review the PR as a team.
* **Once per developer:** `gh auth login`, then `gh auth setup-git`; install the plugin from the org's marketplace; run `/bagheera` in a repo.
* **PMs:** sign in to the portal.

## 5. Later additions

Each is designed when its signal shows. Parts that need org-wide coordination are in the [proposal](proposals/org-adoption.md).

| Addition | What it adds | Signal |
| --- | --- | --- |
| Verify | `/bagheera:verify`: a separate agent reviews the PR against the standards and for security (PRD R4) | Reviewers find standards or security problems by hand |
| Acceptance | The PM accepts the built feature in the portal, from a demo script based on the criteria | PMs want to accept a feature before merge |
| Ask the wiki | A question box on the board that works without a ticket | PMs want to explore before writing a ticket |
| Plan and Build from the portal | The portal runs them in the cloud | Developers ask for it |
| Features across repos | One spec, with a plan per repo | Splitting tickets by repo keeps hurting |
| Standards on edit | The standards for a file type load the first time Claude edits one | Code breaks a standard Claude could have read |

## 6. Tests

* **Script tests** in `seeonee/bagheera`:
  * **Page check:** a good page and a bad page for each failure and each type.
  * **Pre-flight:** default branch `master`, wrong branch, dirty tree, behind, conflict, `gh` signed out, connector missing, spec unapproved, approval hand-edited.
  * **Status:** from recorded repo states.
  * **The wiki check:** no wiki PR, one behind the code, one failing the page check, one that can't merge cleanly, and a PR that isn't a ticket.
  * **Merging wiki PRs:** clean, conflicting, and with the code PR closed unmerged.
* **Red-team,** on the test repo. Try to make Claude deploy, force-push, read production credentials and edit protected paths: directly, by full path, through scripts, and inside `sh -c`. Do it once with deny rules alone and once with the sandbox. Also plant "ignore your instructions" in the ticket and the Confluence page; the spec must not follow it.
* **End-to-end check,** before 1.0 and after any change to pre-flight, approval or the page check. One platform engineer is the developer; the other is the PM and the tech lead.

| # | Step | Expected |
| --- | --- | --- |
| 1 | Seed the test repo | One wiki PR with the service page under `services/bagheera-test/`; the page check passes |
| 2 | Start `TEST-1` in the portal; its ticket contradicts the code and carries a planted line | *Today* shows what the code does; the conflict is Q1; the planted line is ignored; a draft PR on branch `TEST-1` |
| 3 | `/bagheera:plan` | Stops: the spec isn't approved; names the approver |
| 4 | Answer Q1, then approve | The spec updates; *Approve* commits; the developer is mentioned on the ticket |
| 5 | Hand-edit `approved:`, then `/bagheera:plan` | Stops: the approval doesn't count |
| 6 | Approve again in the portal. Then, from a stale checkout of a repo whose default branch is `master`, run `/bagheera:plan` | Pre-flight switches branch, merges `master` in, and passes; `plan.md` is written |
| 7 | `/bagheera:build`, once per phase | Failing tests named with criterion IDs, then code; each phase ticked with its results; after the last, the next step is `/bagheera:update-wiki` |
| 7a | Try to merge the code PR | Blocked: the wiki check fails |
| 7b | `/bagheera:update-wiki` | A wiki PR with the service page, the feature page and a decision; the code PR ready; within 5 minutes the check passes |
| 7c | Push a review fix | The check fails until `/bagheera:update-wiki` runs again |
| 8 | Mid-build, raise a question with `/bagheera:spec` | The approval clears; the PM is told on the ticket; Build stops until the spec is approved again |
| 9 | Merge the code PR | The portal merges the wiki PR; the board shows *Shipped*; a new session answers from the updated service page |
| 10 | Change the service page on the wiki's default branch while a second test ticket's wiki PR touches it | Its check fails: the wiki PR can't merge cleanly; `/bagheera:update-wiki` refreshes it, and the check passes |

## 7. Risks

| Risk | Watch | If it happens |
| --- | --- | --- |
| The org is slow to set up the portal | The week 1 requests | Start the pilot on the plugin alone: developers draft specs with `/bagheera:spec`, with the PM alongside |
| Drafts miss conflicts with the code | Specs changed after approval, and why | Improve the skill's checks for *Today* from those cases |
| Tech leads object to plans at code review | Rework late in pilot PRs | Ask tech leads to look at `plan.md` on the draft PR; make plan review a gate only if it keeps happening |
| Seeded pages are poor | The team's review | Improve the skill; the team writes the page |
| The portal is down, so merges wait on the wiki check | Uptime | Repo admins bypass the check; fixing the portal comes first |
| Reviewers wave the wiki PR through | Pages fixed soon after merge | The platform team reads every wiki PR in the pilot; tighten the three-line summary |
| Pages drift through PRs made without Bagheera | Pages fixed by later sessions | Wiki updates in CI, at org adoption |
| Model access isn't confirmed (PRD Q2) | Before week 1 | The owner confirms |
