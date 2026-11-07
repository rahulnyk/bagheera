# Org adoption

_Status: proposal for the owner's review, 2026-11-07 · [PRD](../prd.md) · [The MVP plan](../plan.md)_

Any team can adopt the [MVP](../plan.md) on its own. This proposal is for an org that adopts Bagheera widely: the parts that need org admins or coordination across teams. They are the PRD's **L** requirements. Each part comes in on its own, when its signal shows. The commands, the approvals and the library's rules stay as they are. [architecture-target.drawio](../architecture-target.drawio) shows the result next to the MVP.

| Part | What it adds | Needs | Brought in when |
| --- | --- | --- | --- |
| **Page ownership and domains** | `domains.yaml` lists each domain, the code repos it covers and its owners, so GitHub requests and requires the right approvers for each domain's pages | Each team's agreement on its owners | Wrong approvals get merged, or nobody can tell who should approve |
| **CI and the GitHub App** (P7, P8) | The automated review on every PR in onboarded repos, required by one org ruleset on repos whose `bagheera` property is `on`; the library update on a schedule. One GitHub App gives CI its GitHub access; its key is one org secret shared with onboarded repos | GitHub org owners: install the App, create the repo property and the ruleset | Developers forget the library update, laptop runs are too slow, or the review must reach every PR in the org (R3) |
| **Claude sign-in for CI** (D3) | Each CI run exchanges its GitHub identity token in the Claude Console for access that expires in minutes; CI has its own Console workspace, billed apart from seats | The Claude admin: a Console workspace, a service account and identity rules | CI comes in |
| **Spend caps** (D4) | Per-run caps (`--max-budget-usd`, `--max-turns`) and a monthly limit on CI's workspace | The Claude admin | CI runs, and their recorded costs show runs worth capping |
| **Managed settings** (P9, R7, D1) | The deny rules, and the sandbox if the pilot chooses it, locked for everyone from the claude.ai admin console, with no file to keep in each repo; the plugin switched on for everyone; other plugins and MCP servers allowed only from the marketplace | A Claude org owner; a Team or Enterprise plan | More than a few teams use Bagheera, keeping every repo's settings file in step gets hard, or someone loosens their own copy |
| **Leadership dashboard** (V1–V4) | An org-level GitHub Project, filled by a scheduled sync from each ticket's PR, with shared views: waiting on me, all stories, by stage, needs attention, shipped | GitHub org owners; CI for the sync | Leadership or PMs ask where features stand across teams |
| **Weekly summary** (V7, R6) | A GitHub issue each Monday: what shipped, what's stuck, overrides, review findings by team and rule, library health, cost by repo; it @mentions a readers team | A readers team, created by GitHub org owners | The dashboard comes in |
| **Adoption and cost by repo** (V6, V8) | Each job's recorded cost added up by repository in the weekly summary, with links to Claude's own dashboards for people and seats | CI's Console workspace | The weekly summary comes in |
| **Slack reminders** | GitHub's scheduled reminders post each team's waiting reviews to its Slack channel | An org owner connects the Slack workspace | Approvals wait because people miss GitHub's notifications |
| **A portable page format** (K10) | Library pages checked against the Open Knowledge Format, so tools beyond Claude Code can read them | Agreement on the format with the tools' owners | Another tool needs to read the library |

## How it comes in

* **One part at a time,** in the order the signals show, each tried with one team first.
* **Teams keep working as they do.** A part changes where something runs or who can change it, never the commands people type.
* **Onboarding a repo** stays under a day of platform effort, with no per-team code (PRD P8).
