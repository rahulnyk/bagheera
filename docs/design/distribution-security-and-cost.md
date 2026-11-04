# Distribution, security and cost

_Status: approved by the owner; current with the decisions of 2026-11-04 · Design doc 6 of 6 · [All design docs](README.md)_

This doc says how the plugin is packaged and versioned, how settings reach laptops, where the jobs run and how CI signs in, how spend is reported, how adoption and cost are shown by repository, and how changes to Bagheera are made. In the pilot, a handful of engineers install the plugin by hand and run everything on their laptops; from rollout, it reaches every laptop and every CI run the same way, with nothing for developers to install. What the first system includes, and when the rest arrives, is in [the plan](../plan.md).

Covers PRD P4, D1–D4 and V6, and the CI side of P7. It builds on [design doc 3](developer-workflow.md), which set the commands and what runs in CI, on [design doc 4](standards-and-review.md), which set the deny rules and the sandbox trial, and on [design doc 5](status-and-dashboard.md), whose weekly summary reports adoption and cost.

## Principles

* **Set once, centrally.** From rollout, developers install nothing and configure nothing (PRD P4).
* **One version everywhere.** Every laptop and every CI run uses the same version (PRD D1, P7).
* **Reviewed before it ships.** Every change is a PR approved by the other platform engineer; publishing it follows the org's plugin marketplace policies (PRD D2).
* **No long-lived keys.** CI signs in with short-lived credentials (PRD D3).
* **Simple for people.** Fewest moving parts, and nothing to learn beyond the commands (design doc 3).

## 1. Packaging and versions

_Decided: [ADR 0087](../decisions/0087-publish-to-the-orgs-plugin-marketplace.md), following [0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md)._

One plugin, published through the org's Claude Code plugin marketplace.

### What's in the repo

```text
seeonee/bagheera
  plugins/bagheera/
    .claude-plugin/plugin.json      the plugin's name, bagheera, and its version
    skills/ agents/ hooks/ …        as set in design docs 1–4
    scripts/ templates/
  settings/
    pilot-user-settings.json        the pilot's deny rules and sandbox settings (section 2)
    managed-settings.json           at rollout, the deny rules for the admin console (section 2)
  .github/workflows/                at rollout, Verify and the dashboard sync (section 3)
```

Commands keep their names, such as `/bagheera:intake` (design doc 3, section 2).

### Versions

* **A new version raises the version** in `plugin.json`, for example from 1.4.0 to 1.5.0. Claude Code fetches a new copy only when the version changes.
* **The org's marketplace publishes it,** under the org's policies: who can install it, the review it needs before publishing, and the lock on other plugins and MCP servers (PRD D1, D2). That's outside Bagheera's scope.
* **It reaches people within about a session.** Claude Code checks for a new version in the background; the running session says `Plugin updated: bagheera`, and the new version loads at the next launch, or straight away with `/reload-plugins`.
* **Rolling back** is a new version that reverts the change, with a higher number.

### What developers need

* **The plugin,** installed from the org's marketplace in the pilot, and switched on for everyone at rollout.
* **A git sign-in that works without a prompt,** because Claude Code fetches in the background. Most developers already have one; otherwise `gh auth login`, then `gh auth setup-git`.
* **Nothing else.** If a fetch ever fails, the plugin keeps working from the last version fetched, and `/plugin` shows the error.

## 2. How settings reach laptops

_Decided: [ADR 0058](../decisions/0058-managed-settings-from-the-admin-console-pilot-installs-by-hand.md) and [0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md), set by the owner._

Bagheera's settings are files in `seeonee/bagheera`, changed by PR (section 6). The deny list is in one file at a time, so it's never kept in two places. A plugin can't carry permission or sandbox settings, so they reach laptops separately from the plugin.

### In the pilot: each engineer's own settings

* **One file to copy from.** `settings/pilot-user-settings.json` holds the deny rules (design doc 4, section 5), bypass mode off, the sandbox settings for the trial, and a rule letting Claude read the local library copy without prompts. At setup, each pilot engineer adds its contents to `~/.claude/settings.json`; the platform team helps anyone who already has settings there.
* **What holds.** Deny rules win over allow rules from any settings file, so a repo's own settings can't loosen them. With `failIfUnavailable`, Claude Code won't start if the sandbox can't run; with `allowUnsandboxedCommands` off, Claude can't ask to run a command outside it.
* **What holds only by agreement.** The engineer can change the file. The list of allowed hosts can only be locked in managed settings, so an unlisted host prompts the engineer instead of being blocked. Those prompts show which hosts the trial needs.
* **When the deny list changes,** the platform team asks the pilot engineers to copy the file again.

### At rollout: managed settings from the claude.ai admin console

* **A Claude org owner pastes `settings/managed-settings.json`** into the admin console (Organization settings, then Claude Code, then Managed settings). It holds the deny rules and bypass mode off; whether it also turns on the sandbox is the pilot exit decision (design doc 4, section 5).
* **Claude Code fetches it** at startup and checks hourly, so a change reaches everyone within about an hour.
* **One policy for everyone** in the company's Claude org. Pilot machines need nothing redone.
* **It needs a Team or Enterprise plan** (PRD Q7).
* **It's a guard on the developer's machine, not a hard barrier.** It doesn't reach someone signed in with a personal account or an API key. GitHub's own rules still block force-push and changes to protected paths on the server (design doc 4, section 5).

## 3. Where the jobs run

Three jobs work beyond one developer's session: the library update (design doc 1, section 5), Verify (design doc 4, section 4) and the dashboard update (design doc 5). In the pilot they run on laptops, from commands. At rollout the same commands run in CI.

### In the pilot: on laptops, from commands

_Decided: [ADR 0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) and [0080](../decisions/0080-developers-run-the-library-update-after-each-merge.md)._

| Job | Command | Who runs it | When |
| --- | --- | --- | --- |
| Library update | `/bagheera:update-library` | The developer; the platform team catches up | Once their ticket's PR has merged |
| Verify, once added | `/bagheera:verify` | The developer | Before marking the PR ready, and again after any push |
| Dashboard update, once added | `/bagheera:update-dashboard` | A manager | On demand, every few days |

* **Own access only.** Each command runs with the person's own Claude seat and GitHub sign-in. The pilot has no CI workflow for Claude, no Console setup and no GitHub App.
* **Verify stays separate from the author** (PRD R3). The command runs the review as a separate Claude Code process (`claude -p`) that never sees the author's conversation, posts its findings on the PR, and marks the commit `bagheera/verify` passed or failed. Each pilot repo's branch rules require that mark; the repo's admins set this once.
* **Baloo stays boxed in.** Scripts gather and open PRs with the runner's GitHub access; Baloo edits only a copy of the library, with no shell, no web and no GitHub access.
* **Freshness.** The library is as fresh as the last merge's update, and the dashboard as fresh as its last run. Approval requests still reach people straight away, because the stage commands request reviewers themselves.

### At rollout: the same commands in CI

* **Verify** runs from `seeonee/bagheera` at a release tag: the org ruleset names that workflow and tag, so every repo runs the released version.
* **The scheduled jobs** are the library update, a workflow in the library repo, and the dashboard sync, a workflow in `seeonee/bagheera`. Each first checks out `seeonee/bagheera` at the latest release tag and runs from there.
* **The plugin in CI** comes from the org's marketplace through the Claude Code GitHub Action's plugin inputs, so a laptop and a CI run on the same day run the same version.

### At rollout: how CI signs in to Claude

_Decided: [ADR 0061](../decisions/0061-ci-signs-in-to-claude-with-workload-identity-federation.md)._

* **Nothing is stored.** Each run asks GitHub for a signed identity token, which says which repo and which workflow it is. The Claude Code GitHub Action exchanges it in the Claude Console for access that expires in minutes. The workflow only needs GitHub's `id-token: write` permission, and the IDs it passes aren't secrets.
* **Only our workflows qualify.** The Console checks each token against two rules:

  | Rule | Admits | Used by |
  | --- | --- | --- |
  | Verify | The workflow `seeonee/bagheera/.github/workflows/verify.yml` at a release tag, in any `seeonee` repo | Verify, on every PR |
  | Library update | The library update workflow in the library repo, on its default branch; only the platform team can change it | The library update |

  A workflow someone writes in a product repo matches neither rule, so it can't get Claude access.
* **Access ends with the job.** Each token's lifetime is set to the job's time limit, and its scope allows calling Claude and nothing else in the Console.
* **CI has its own Console workspace,** billed per use and reported apart from the seats laptops use (PRD D4).
* **One-time setup by the Claude admin,** in the Console's identity federation wizard: GitHub Actions as a trusted issuer, a service account, the workspace and the two rules.
* **One thing to test before rollout.** The Verify rule relies on GitHub naming the called workflow in the token of a ruleset-required run. If it doesn't, the rule falls back to admitting runs in `seeonee` repos; the recorded costs would show any misuse.

### At rollout: one GitHub App

_Decided: [ADR 0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) and [0063](../decisions/0063-one-github-app-key-shared-with-onboarded-repos.md), set by the owner._

* **One App gives the CI jobs their GitHub access:** reading the library and the plugin, opening library PRs, writing the dashboard and the weekly summary, posting results and re-running Verify. Each run gets an access token from it that lasts an hour.
* **Its key is one organisation secret,** shared with the repos whose `bagheera` property is `on`, so Verify can use it where it runs.
* **Accepted on purpose:** a workflow written in an onboarded repo could read the key and act as Bagheera. That takes a deliberate, reviewed change to the repo's workflows, and everything the App does is on record in GitHub.

## 4. Spend limits and reporting

_Decided: [ADR 0068](../decisions/0068-no-spend-caps-until-after-the-pilot.md), set by the owner._

No spend caps for now. Whether to have one, and at what level, is decided after the pilot from real costs.

* **Reported, not capped.** Each Verify and library-update run records its cost, and the weekly summary adds them up by repository (section 5). At rollout, CI's spend also shows on its own Console workspace, apart from seats.
* **In the pilot,** runs count against people's own seats, under whatever limits the company's Claude plan already sets.
* **After the pilot:** cap or not ([the plan](../plan.md#decisions-deferred-to-the-pilot-exit)). If yes, Claude Code's own `--max-budget-usd` and `--max-turns` cap each run, and a limit on CI's Console workspace caps each month; both are settings, with nothing to build.

## 5. Adoption and cost by repository

_Decided: [ADR 0066](../decisions/0066-report-job-spend-by-repository.md), set by the owner._

Spend and adoption are reported by repository, the simplest unit: each job already runs for one repo.

### Spend

* **Each run records its own cost.** Claude Code reports a run's estimated cost when it finishes. Verify writes it next to its findings, for example `Verify passed · 2 advisory findings · $0.42`; the library update writes it in its PR.
* **The same on laptops.** `/bagheera:verify` runs the review as a separate Claude Code process, the same way CI does, so its cost is known. In the pilot sandbox, that process needs Anthropic's API on the allowed hosts.
* **The weekly summary adds them up** (design doc 5, section 6): Verify spend per repo, library-update spend, and Verify spend per merged PR (PRD §7).
* **The cost is Claude Code's estimate at API prices.** At rollout, CI's real bill is on the Console's cost page. For people on seats, it measures relative cost, not what the company pays.

### Adoption

* **By repo, from GitHub,** in the weekly summary: stories started and shipped, Bagheera PRs merged, and who used the stage commands that week.
* **Everything else stays in Claude's own dashboards,** linked from the weekly summary: active users, sessions and spend per person on claude.ai, and CI's workspace total on the Console.

## 6. Changing Bagheera

_Decided: [ADR 0067](../decisions/0067-review-changes-by-pr-with-code-owners.md), [0086](../decisions/0086-no-code-owners-approvers-recorded-and-checked-by-the-merger.md) and [0087](../decisions/0087-publish-to-the-orgs-plugin-marketplace.md)._

1. **A PR to `main`,** approved by the other platform engineer. `main` needs a PR and doesn't allow force-push.
2. **Try it.** A platform engineer loads `main` from a checkout with `claude --plugin-dir plugins/bagheera` and runs the changed commands on the test repo. The Spec Kit review each quarter takes this path too (design doc 3, section 1).
3. **Publish.** Raise the version (section 1); the org's marketplace publishes it under its policies.
4. **Roll back** with a new version (section 1).

* **Settings files need more.** A change to `settings/` also needs security's approval, and the architecture council's for the deny rules; the author asks for them, and whoever merges checks.
* **No fast lane.** Urgent fixes take the same path; it's short.
* **When one platform engineer is away,** changes wait for them.
