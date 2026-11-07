# Design docs

The six design docs describe Bagheera's MVP: how each area works, in enough detail to build it. [The plan](../plan.md) says what's built and in what order, the pilot, and what a team can add later; the [org-adoption proposal](../proposals/org-adoption.md) holds the parts that need org-wide coordination. The [PRD](../prd.md) is the source of truth for what Bagheera must do, and the [decisions](../decisions/) record why it's designed this way.

**Where things stand (2026-11-07).** The six docs, the plan and the proposal are drafts for the owner's review.

## The docs

| # | Doc | What it settles | PRD requirements |
| --- | --- | --- | --- |
| 1 | [`knowledge-library.md`](knowledge-library.md) | Each team's library: layout and page format, shelf life, what it learns and when, how it's kept current, approved, merged and checked, and how sessions read it | K1–K7, K9 |
| 2 | [`seeding.md`](seeding.md) | How the seeding skill drafts the first service pages, and how they're reviewed | K8 |
| 3 | [`developer-workflow.md`](developer-workflow.md) | The commands, what each writes, approval and overrides, open questions, Build, and where it runs | P1–P6, C1–C4 |
| 4 | [`standards-and-forbidden-actions.md`](standards-and-forbidden-actions.md) | Rules within a standard, writing standards, how sessions see them, and blocking forbidden actions | R1, R2, R4 |
| 5 | [`status-and-notifications.md`](status-and-notifications.md) | Where status comes from, what `/bagheera` says, notifications and the Jira link | P3, C5 |
| 6 | [`distribution-security-and-cost.md`](distribution-security-and-cost.md) | Packaging and versions, the settings file, where the work runs, cost, and changing Bagheera | P4, D2 |

## Moving parts

A block-level diagram of these parts and how they connect is in [components.drawio](../components.drawio); open it in the draw.io app or at app.diagrams.net.

### What the platform team builds

| Part | What it is |
| --- | --- |
| `seeonee/bagheera` | The plugin's source, and the library template |
| The plugin | Six commands, `seed-service-page`, Baloo and a read-only code reader, the session-start hook, scripts and templates, and the Atlassian connector |
| The library template | The four page folders, `index.md`, the format check with its workflow, and the settings file |

### What a team sets up

| Part | What it is |
| --- | --- |
| The team's library, Council Rock | One repo, made from the template, with its branch rule and the team's settings file |
| Each person's install | The plugin from the org's marketplace, and the settings file in their own settings |
| Its repos | A branch rule blocking force-push on main; each seeded before its first ticket |

No servers, nothing self-hosted, and nothing from org admins.

### What we need from others

| Who | What | When |
| --- | --- | --- |
| The org's Claude Code plugin marketplace, under the org's policies (assumed) | Publish each Bagheera version and let teams install it | Each version |
| Security | Approve changes to the settings file | When it changes |
| PMs and tech leads | Approve stage documents and library PRs | Ongoing |
| Managers | Hold teams to who approves what | Ongoing |

Jira needs no admin work: developers' own access is enough.

### Outside services

| Service | Used for |
| --- | --- |
| GitHub | Code, PRs, approvals, and the library's format check |
| Jira and Confluence | Ticket text for Intake; the pages standards are seeded from |
| Anthropic | Claude: Team or Enterprise seats on laptops |

## Open items

| Item | Settled |
| --- | --- |
| The sandbox for everyone, for some repos, or deny rules only ([0022](../decisions/0022-deny-rules-in-each-repo-and-a-sandbox-trial.md)) | At the end of the pilot |
| Claude plan and model access (PRD Q7, Q8) | The owner, before week 1 |
| Jungle Book names ([0006](../decisions/0006-plain-names-a-few-themed-ones.md)) | The naming register, as components come up |

## Naming register

Themed names are proposed to the owner first ([0006](../decisions/0006-plain-names-a-few-themed-ones.md)). Commands, files, workflows and skills keep plain names.

| Component | Plain name | Themed name | Status |
| --- | --- | --- | --- |
| The product | Delivery harness | Bagheera | Approved (PRD) |
| The plugin, which prefixes every command | Delivery harness plugin | `bagheera` (commands are `/bagheera:<command>`) | Approved ([0015](../decisions/0015-commands-under-the-bagheera-plugin.md)) |
| A team's knowledge library | Knowledge library | Council Rock | Approved ([0010](../decisions/0010-name-council-rock-and-baloo.md)) |
| The library writer | Knowledge agent | Baloo | Approved ([0010](../decisions/0010-name-council-rock-and-baloo.md)) |
