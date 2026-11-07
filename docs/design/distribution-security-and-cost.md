# Distribution, security and cost

_Design doc 6 of 6 · [All design docs](README.md)_

This doc says how the plugin is packaged and versioned, what the settings file holds and how it reaches laptops, where the work runs, what it costs, and how changes to Bagheera are made. Developers install the plugin from the org's marketplace, each repo commits the settings file, and everything runs on laptops, with nothing from org admins.

Covers PRD P4 and D2. It builds on [design doc 3](developer-workflow.md), which set the commands, and on [design doc 4](standards-and-forbidden-actions.md), which set the deny rules and the sandbox trial.

## Principles

* **One version everywhere.** Every laptop runs the version the marketplace publishes (PRD P4).
* **Reviewed before it ships.** Every change is a PR approved by the other platform engineer; publishing it follows the org's plugin marketplace policies (PRD D2).
* **Nothing from org admins.** A team adopts Bagheera on its own.
* **Simple for people.** Fewest moving parts, and nothing to learn beyond the commands (design doc 3).

## 1. Packaging and versions

_Decided: [ADR 0034](../decisions/0034-publish-through-the-org-marketplace.md) and [0015](../decisions/0015-commands-under-the-bagheera-plugin.md)._

One plugin, published through the org's Claude Code plugin marketplace.

### What's in the repo

```text
seeonee/bagheera
  plugins/bagheera/
    .claude-plugin/plugin.json      the plugin's name, bagheera, and its version
    skills/ agents/ hooks/ …        as set in design docs 1–3
    scripts/ templates/
  settings/
    repo-settings.json              the settings template each repo commits (section 2)
```

### Versions

* **A new version raises the version** in `plugin.json`, for example from 1.4.0 to 1.5.0. Claude Code fetches a new copy only when the version changes.
* **The org's marketplace publishes it,** under the org's policies: who can install it, the review it needs before publishing, and any lock on other plugins. That's outside Bagheera's scope.
* **It reaches people within about a session.** Claude Code checks for a new version in the background; the running session says `Plugin updated: bagheera`, and the new version loads at the next launch, or straight away with `/reload-plugins`.
* **Rolling back** is a new version that reverts the change, with a higher number.

### What developers need

* **The plugin,** installed from the org's marketplace, with auto-update on.
* **A git sign-in that works without a prompt,** because Claude Code fetches in the background. Most developers already have one; otherwise `gh auth login`, then `gh auth setup-git`.
* **Nothing else.** The repo's committed settings file (section 2) applies on its own.

If a fetch ever fails, the plugin keeps working from the last version fetched, and `/plugin` shows the error.

## 2. The settings file

_Decided: [ADR 0022](../decisions/0022-deny-rules-in-each-repo-and-a-sandbox-trial.md), set by the owner._

Claude Code won't take permission or sandbox settings from a plugin. Without managed settings, which need an org admin, the place everyone in a repo shares is the repo's own `.claude/settings.json`.

* **One template.** `settings/repo-settings.json` in `seeonee/bagheera` holds the deny rules (design doc 4, section 4), bypass mode off, and a rule letting Claude read the local library copy without prompts. The pilot team's repos also turn on the sandbox trial.
* **Committed in each repo.** When a repo is seeded, its team commits the template as `.claude/settings.json`, merging it into any settings the repo already has, and may add deny rules of its own. Developers copy nothing.
* **What holds.** Deny rules win over allow rules from any settings file, so nobody's own settings can loosen them. Changes to the committed file arrive only through a reviewed PR. With `failIfUnavailable`, Claude Code won't start if the sandbox can't run; with `allowUnsandboxedCommands` off, Claude can't ask to run a command outside it.
* **What holds only by agreement.** A developer can edit the file in their own checkout without committing it. An unlisted host prompts the developer instead of being blocked; those prompts show which hosts the sandbox needs.
* **When the template changes,** the platform team asks each team to update its repos.

## 3. Where the work runs

_Decided: [ADR 0024](../decisions/0024-laptop-commands-and-the-library-update.md)._

Everything runs on laptops, from commands, with the person's own Claude seat and GitHub sign-in. The only workflow is the library's format check, a script with no secrets (design doc 1, section 7).

| Work | Command | Who runs it | When |
| --- | --- | --- | --- |
| The stage commands | `/bagheera`, `/bagheera:intake`, `/bagheera:plan`, `/bagheera:build` | The developer | Through the ticket |
| Library update | `/bagheera:update-library` | The developer; anyone on the team catches up | Once their ticket's PR has merged |
| Seeding | `seed-service-page` | An engineer on the team | Before a repo's first ticket |
| Standards | `/bagheera:standards` | The architecture council | When a standard is seeded, added or changed |

* **Baloo stays boxed in.** Scripts gather and open PRs with the runner's GitHub access; Baloo edits only a copy of the library, with no shell, no web and no GitHub access.
* **Freshness.** The library is as fresh as the last merge's update. Approval requests reach people straight away, because the commands request reviewers themselves.

## 4. Cost

* **Each run records its cost.** Claude Code reports a run's estimated cost when it finishes, and the library update writes it in its PR. The platform team notes these through the pilot.
* **On people's own seats.** Runs count against each person's Claude seat, under whatever limits the company's Claude plan sets (PRD Q7). There are no caps.
* **The cost is Claude Code's estimate at API prices.** For people on seats, it measures relative cost, not what the company pays.

## 5. Changing Bagheera

_Decided: [ADR 0025](../decisions/0025-review-changes-to-bagheera-by-pr.md) and [0034](../decisions/0034-publish-through-the-org-marketplace.md)._

1. **A PR to `main`,** approved by the other platform engineer. `main` needs a PR and doesn't allow force-push.
2. **Try it.** A platform engineer loads `main` from a checkout with `claude --plugin-dir plugins/bagheera` and runs the changed commands on the test repos. The Spec Kit review each quarter takes this path too (design doc 3, section 1).
3. **Publish.** Raise the version (section 1); the org's marketplace publishes it under its policies.
4. **Roll back** with a new version (section 1).

* **The settings template needs more.** A change to it also needs security's approval, and the architecture council's for its deny rules; the author asks for them, and whoever merges checks.
* **No fast lane.** Urgent fixes take the same path; it's short.
* **When one platform engineer is away,** changes wait for them.
