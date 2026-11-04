# Distribution, security and cost

_Status: in progress · Design doc 6 of 6 · [All design docs](README.md)_

Bagheera reaches every laptop and every CI run the same way, with nothing for developers to install, and changes to it are as carefully reviewed as production code. This doc says how the plugin is packaged and released, how settings reach laptops, how CI signs in, how spend is capped and reported, how adoption and cost are shown by team, and how changes to Bagheera are reviewed and released.

Covers PRD P4, D1–D4 and V6, and the CI side of P7. It builds on [design doc 3](developer-workflow.md), which set the commands and what runs in CI, on [design doc 4](standards-and-review.md), which set the deny rules and the sandbox trial, and on [design doc 5](status-and-dashboard.md), whose weekly summary reports adoption and cost.

## Principles

* **Set once, centrally.** Developers install nothing and configure nothing (PRD P4).
* **One version everywhere.** Every laptop and every CI run uses the same release (PRD D1, P7).
* **Reviewed like production code.** Every change to Bagheera is reviewed, and security owns the parts that run on laptops (PRD D2).
* **No long-lived keys.** CI signs in with short-lived credentials (PRD D3).
* **Simple for people.** Fewest moving parts, and nothing to learn beyond the commands (design doc 3).

## 1. Packaging and releases

_Decided: [ADR 0057](../decisions/0057-release-the-plugin-from-a-release-branch.md), following [0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md)._

One plugin, in one marketplace, released from one branch. Everything here is Claude Code's own plugin system; nothing is built.

### What's in the repo

```text
seeonee/bagheera
  .claude-plugin/marketplace.json   the marketplace, named seeonee; it lists one plugin
  plugins/bagheera/
    .claude-plugin/plugin.json      the plugin's name, bagheera, and its version
    skills/ templates/ scripts/ …   as set in design docs 1–4
  .github/workflows/                Verify, the curation run, the dashboard sync
```

The marketplace file lists the plugin by its folder:

```json
{
  "name": "seeonee",
  "owner": { "name": "Platform team" },
  "plugins": [
    { "name": "bagheera", "source": "./plugins/bagheera" }
  ]
}
```

The plugin's install name is `bagheera@seeonee`. Commands keep their names, such as `/bagheera:intake` (design doc 3, section 2).

### Set once, for everyone

The Claude admin adds these to the managed settings once (how managed settings reach laptops is section 2):

```json
{
  "extraKnownMarketplaces": {
    "seeonee": {
      "source": { "source": "github", "repo": "seeonee/bagheera", "ref": "release" },
      "autoUpdate": true
    }
  },
  "enabledPlugins": { "bagheera@seeonee": true }
}
```

* **The marketplace is added on every machine,** from the `release` branch.
* **The plugin is switched on for everyone,** and a developer can't switch it off: managed settings win over their own.
* **Updates arrive on their own.** Auto-update is off by default for a company's own marketplace; this turns it on. If the managed settings also stop Claude Code's own updates (`DISABLE_AUTOUPDATER`), they must set `FORCE_AUTOUPDATE_PLUGINS` too, or plugin updates stop with them.

### Releasing a version

1. **Changes merge to `main`** after review (section 6).
2. **A release is a PR from `main` to `release`** that raises the version in `plugin.json`, for example from 1.4.0 to 1.5.0. Its description lists what changed. A check fails the PR if the version doesn't go up, because Claude Code fetches a new copy only when the version changes.
3. **It reaches everyone within about a session.** Within ten minutes of the first message in a session, Claude Code checks for a new version in the background. The running session says `Plugin updated: bagheera`, and the new version loads at the next launch, or straight away with `/reload-plugins`.
4. **CI uses the same branch,** so a CI run and a laptop on the same day run the same version (section 3).

**Rolling back** is a new release: revert the change on `release` and raise the version again, for example from 1.5.0 to 1.5.1. Version numbers only go up, so there's never a question of which one is newer.

### What developers need

* **Read access to `seeonee/bagheera`.** Claude Code fetches the marketplace with the developer's own git sign-in, and has no token of its own. The GitHub org owners give every engineer read access, once.
* **A git sign-in that works without a prompt.** Claude Code fetches in the background and never asks for a password. Most developers already have this from cloning repos: an SSH key loaded in their agent, or a stored HTTPS credential. The onboarding guide has a one-line check, and the usual fix if it fails (`gh auth login`, then `gh auth setup-git`).
* **Nothing else.** If a fetch ever fails, the plugin keeps working from the last version fetched, and `/plugin` shows the error.

## 2. How settings reach laptops

_Decided: [ADR 0058](../decisions/0058-managed-settings-from-the-admin-console-pilot-installs-by-hand.md), set by the owner._

Bagheera's settings are files in `seeonee/bagheera`, changed by PR and reviewed like code (section 6). During the pilot there is one, `settings/pilot-user-settings.json`, which pilot engineers copy into their own settings. At rollout it becomes `settings/managed-settings.json`, which goes into the admin console and holds the plugin switch (section 1), the deny rules (design doc 4, section 5) and the lock on other plugins and MCP servers (below). The deny list is in one file at a time, so it's never kept in two places.

### During the pilot: installed by hand

Nothing is set in the admin console. Each pilot engineer, on a Mac, installs the plugin once:

```bash
claude plugin marketplace add seeonee/bagheera#release
claude plugin install bagheera@seeonee
```

Then, in `/plugin`, under **Marketplaces**, they select `seeonee` and turn on auto-update, so fixes during the pilot arrive on their own (section 1).

* **The same plugin and the same `release` branch** as everyone gets later, so the pilot tests what ships.
* **Read access** to `seeonee/bagheera` goes to the pilot engineers first, and to everyone at rollout.
* **The deny rules and the sandbox trial** can't come with the plugin, because a plugin can't carry permission or sandbox settings. They go in each engineer's own settings (below).

### The pilot's deny rules and sandbox

_Decided: [ADR 0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md), set by the owner._

Only a handful of trusted engineers take part, so their own settings file is enough.

* **One file to copy from.** `seeonee/bagheera` holds `settings/pilot-user-settings.json`: the deny rules (design doc 4, section 5), bypass mode off, and the sandbox settings for the trial (design doc 4, section 5). At setup, each pilot engineer adds its contents to `~/.claude/settings.json`; the platform team helps anyone who already has settings there.
* **What still holds.**
  * Deny rules win over allow rules from any settings file, so a repo's own settings can't loosen them.
  * With `failIfUnavailable`, Claude Code won't start if the sandbox can't run. With `allowUnsandboxedCommands` off, Claude can't ask to run a command outside it.
* **What doesn't.**
  * The engineer can change the file; the pilot relies on their agreement not to.
  * The list of allowed hosts can only be locked in managed settings. In the pilot, an unlisted host prompts the engineer instead of being blocked. Those prompts are useful: they show which hosts the trial needs.
* **When the deny list changes** during the pilot, the platform team asks the pilot engineers to copy the file again.
* **At rollout,** the deny rules move into the console's settings file, where developers can't change them. Whether the sandbox goes there too is the pilot exit decision (design doc 4, section 5).

### After the pilot: the claude.ai admin console

* **A Claude org owner pastes the merged settings file** into the admin console (Organization settings, then Claude Code, then Managed settings).
* **Claude Code fetches it** at startup and checks hourly, so a change reaches everyone within about an hour, including Claude Code on the web.
* **One policy for everyone** in the company's Claude org.
* **Nothing to redo on pilot machines.** The console's settings register the same marketplace by name, take over from the hand-made install, and switch the plugin on for good.
* **It needs a Team or Enterprise plan** (PRD Q7); the design assumes one.
* **It's a guard on the developer's machine, not a hard barrier.** It doesn't reach someone signed in with a personal account or an API key. GitHub's own rules still block force-push and changes to protected paths on the server (design doc 4, section 5).

### What the settings lock at rollout

_Decided: [ADR 0060](../decisions/0060-lock-plugins-and-mcp-servers-leave-own-files.md)._

Engineers can't add unapproved extensions, and everyone runs the same version (PRD D1). The lock covers what runs code from outside: plugins and MCP servers. It needs managed settings, so it starts at rollout; in the pilot nothing is locked.

| Kind | At rollout |
| --- | --- |
| **Plugins** | Only from our marketplace, `seeonee/bagheera` at `release`. The flags that load a plugin for one session are rejected, and plugins synced from claude.ai don't load in Claude Code |
| **MCP servers** (connections to outside tools) | Only those on the approved list, starting with the Jira connector. Claude Code's built-in ones, such as the IDE connection, still work |
| **Hooks, plain skills, agents, CLAUDE.md files** in repos and personal settings | Unchanged. They're the engineer's or the team's own, reviewed in their own repos |

In the console's settings file, alongside the plugin switch (section 1) and the deny rules:

```json
{
  "strictKnownMarketplaces": [
    { "source": "github", "repo": "seeonee/bagheera", "ref": "release" }
  ],
  "disableSideloadFlags": true,
  "syncClaudeAiPlugins": false,
  "allowManagedMcpServersOnly": true,
  "allowedMcpServers": [
    { "serverUrl": "https://mcp.atlassian.com/*" }
  ]
}
```

* **Adding an extra plugin.** Someone asks for it, for example one of Anthropic's own; it's reviewed (section 6) and added to our marketplace as an entry pinned to an exact commit. Anyone can then install it with `claude plugin install <name>@seeonee`; only `bagheera` is switched on for everyone.
* **Adding an MCP server.** The same way: reviewed, then added to `allowedMcpServers` by its address.
* **What people see if they try something else.** Adding another marketplace, or starting Claude Code with `--plugin-dir`, fails with a message naming the company's policy.
* **Plugins people already installed** from other marketplaces stop loading at rollout. The rollout note says so in advance, with how to ask for one to be added.

## 3. Where the jobs run

Three jobs work beyond one developer's session: Verify, on every phase PR (design doc 3, section 9); the library update, which is the curation run with Close and the weekly check (design doc 1, sections 5, 6 and 8); and the dashboard update, which is the sync with the criteria results table and the weekly summary (design doc 5). In the pilot they run on laptops, from commands. At rollout the same commands run in CI.

### In the pilot: on laptops, from commands

_Decided: [ADR 0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md), set by the owner._

| Job | Command | Who runs it | When |
| --- | --- | --- | --- |
| Verify | `/bagheera:verify` | The developer | Before marking a phase PR ready, and again after any push |
| Library update: updates from merged PRs, Close, bookkeeping, and the weekly check when one is due | `/bagheera:update-library` | A manager | On demand, every few days |
| Dashboard update: status, assignees, the criteria results table, and the weekly summary when one is due | `/bagheera:update-dashboard` | A manager | On demand, every few days |

* **Own access only.** Each command runs with the person's own Claude seat and GitHub sign-in. The pilot has no CI workflow, no Console setup and no GitHub App.
* **Verify stays separate from the author** (PRD R3). The command reviews in a fresh Claude that never sees the author's conversation, posts its findings on the PR, and marks the commit `bagheera/verify` passed or failed. Each pilot repo's branch rules require that mark before merging; the repo's admins set this once. A mark belongs to one commit, so after a new push the PR can't merge until Verify runs again.
* **Baloo stays boxed in.** `/bagheera:update-library` runs the curation run's steps (design doc 1, section 6). Scripts gather and sort with the manager's GitHub access; Baloo edits only a copy of the library, with no shell, no web and no GitHub access. Library PRs come from the manager's account, and page owners still approve them.
* **Managers who run them** install the plugin like pilot engineers (section 2), and need write access to the library repo and the dashboard project, and read access to the pilot repos.
* **What the pilot gives up:**
  * The dashboard and the library are only as fresh as the last run. Approval requests still reach people straight away, because the stage commands request reviewers themselves (design doc 5, section 5); only the sync's assignments wait.
  * Verify holds through the repo's branch rule and the developer's agreement, and its cost shows in each developer's own usage, so the PRD's "CI cost per merged PR" isn't measured in the pilot.
  * PRD G1's pilot target, service pages updated within a day of a merge, needs the library update at least daily (an open item in [the design index](README.md)).

### At rollout: the same commands in CI

* **Verify** runs from `seeonee/bagheera` at `release`: the org ruleset names that branch, so every repo runs the released workflow.
* **The scheduled jobs** are the curation run, a workflow in the library repo (design doc 1, section 6), and the dashboard sync, a workflow in `seeonee/bagheera`. GitHub always starts scheduled workflows from the repo's default branch, so each one first checks out `seeonee/bagheera` at `release` and runs the plugin and scripts from there.
* **The plugin in CI** is installed from the same marketplace and branch as on laptops (section 1), through the Claude Code GitHub Action's plugin inputs. So a laptop and a CI run on the same day run the same version.

### At rollout: how CI signs in to Claude

_Decided: [ADR 0061](../decisions/0061-ci-signs-in-to-claude-with-workload-identity-federation.md)._

* **Nothing is stored.** Each run asks GitHub for a signed identity token, which says which repo and which workflow it is. The Claude Code GitHub Action exchanges it in the Claude Console for access that expires in minutes. The workflow only needs GitHub's `id-token: write` permission, and the IDs it passes aren't secrets.
* **Only our workflows qualify.** The Console checks each token against two rules:

  | Rule | Admits | Used by |
  | --- | --- | --- |
  | Verify | The workflow `seeonee/bagheera/.github/workflows/verify.yml` at `release`, in any `seeonee` repo | Verify, on every PR |
  | Curation run | The curation workflow in the library repo, on its default branch; only the platform team can change it | The curation run |

  A workflow someone writes in a product repo matches neither rule, so it can't get Claude access.
* **Access ends with the job.** Each token's lifetime is set to the job's time limit, and its scope allows calling Claude and nothing else in the Console.
* **CI has its own Console workspace,** billed per use and reported apart from the seats laptops use (PRD D4). Its spend cap is section 4.
* **One-time setup by the Claude admin,** in the Console's identity federation wizard: GitHub Actions as a trusted issuer, a service account, the workspace and the two rules.
* **One thing to test before rollout.** The Verify rule relies on GitHub naming the called workflow in the token of a ruleset-required run. If it doesn't, the rule falls back to admitting runs in `seeonee` repos, and the workspace's spend cap bounds any misuse.

### At rollout: one GitHub App

_Set by the owner ([ADR 0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md))._

* **One App gives the CI jobs their GitHub access:** reading the library and the plugin, opening library PRs, writing the dashboard and the weekly summary, posting results and re-running Verify. Each run gets an access token from it that lasts an hour.
* **Its key is never readable from product repos.** Verify runs there, and any workflow in a repo can read the secrets shared with it. How Verify gets its GitHub access without that is settled in the rollout plan (`docs/plan.md`).

## Still to come

4. Spend limits and reporting (D4)
5. Adoption and cost by team (V6)
6. How changes to Bagheera are reviewed and released (D2)
