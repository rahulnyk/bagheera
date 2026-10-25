# Bagheera — Product Requirements

_Status: draft v2 for review · Owner: RN · 2026-10-25_

## 1. Summary

Bagheera is a delivery harness on Claude Code for an engineering org of about 100 engineers. It gives every developer one clear way to take a feature from a Jira ticket to merged, reviewed, demoed code. Along the way it keeps the org's knowledge current, applies the org's standards automatically, brings PMs into the work as it happens, and shows leadership where everything stands.

It is built mostly from tools that already exist (Claude Code, GitHub Spec Kit, claude-code-action, GitHub, Jira); we build only the glue that's missing.

## 2. Problem

* **Knowledge is scattered and decays.** Standards, service details, past decisions and known pitfalls live in people's heads, old docs and chat logs. Claude starts every session knowing none of it.
* **Standards are applied unevenly.** Each team and each engineer interprets the standards differently; reviewers catch some issues, late and inconsistently.
* **Every developer works with AI differently.** There's no shared path from ticket to merged code. Specs, designs and test plans are optional and live in chats nobody else sees. New joiners don't know where to start.
* **PMs give feedback late and in meetings.** They see a ticket and, weeks later, a demo. Questions get answered in syncs and status meetings rather than where the work happens.
* **Leadership lacks a clear view.** Where each feature is, what's stuck, and what AI tooling costs and delivers is assembled by hand.

## 3. Goals

These are the five goals Bagheera exists for. Every requirement in §6 serves at least one.

| ID | Goal | What success looks like |
| --- | --- | --- |
| **G1** | **Knowledge management across the org** | One shared knowledge base of standards, services, decisions and shipped features that updates itself from merged work, and that Claude reads at the start of every session |
| **G2** | **Enforce standards across the org** | Standards are written once by their owners and applied everywhere: loaded into every session, checked on every PR, with dangerous actions blocked outright |
| **G3** | **A standard, confusion-free way for any developer to be highly productive** | Any engineer, in any repo, follows the same few commands from ticket to merged code, always knows the next step, and needs no setup |
| **G4** | **PMs inside the development loop, with fast feedback** | PMs review and approve the spec, acceptance criteria and demo where the work happens, within hours not weeks, replacing many weekly status and clarification meetings |
| **G5** | **Visibility for leadership** | One place showing every story's stage, what's waiting on whom, throughput, adoption and cost by team, without anyone compiling a report |

### Non-goals

* Deploying anything. Existing CD deploys on merge; every production change keeps a human approval.
* Replacing Jira as the ticket system, or GitHub as the code and review system.
* A general multi-agent framework (LangGraph, CrewAI, AutoGen) or a custom agent runtime.
* Full autonomy. Building stays human-steered for the pilot.
* Ops tools (e.g. Grafana) as the interface for PMs or leadership.

## 4. Users

| User | Needs | Touchpoints |
| --- | --- | --- |
| **Engineer** (~100) | One obvious path from ticket to merge; context already loaded; no ceremony that doesn't pay for itself | Claude Code (terminal, IDE, desktop), GitHub PRs |
| **Product manager** | Shape the spec early, answer questions quickly, approve acceptance criteria and demos, see what's waiting on them | Dashboard, GitHub PR review, Jira |
| **Tech lead** | Approve architecture and plan; trust that standards are applied | GitHub PR review, dashboard |
| **QA** | Approve test cases; see acceptance criteria tied to automated tests | GitHub PR review, dashboard |
| **Engineering leadership** | Status, throughput, bottlenecks, adoption and cost | Dashboard, weekly summary |
| **Standards owners** (senior engineers, security) | Own the standards and rules; approve any change to them | Knowledge base PRs |
| **Platform team** (2 engineers) | Run, change and roll out Bagheera safely | Bagheera's repos, admin consoles |

## 5. Feature lifecycle

Each stage writes a markdown artifact into the feature's repo, in a folder named after the ticket, and opens or updates a PR. The next stage won't start until the right person has approved.

| # | Stage | Output | Approved by | Goals |
| --- | --- | --- | --- | --- |
| 1 | Intake | Spec from the Jira ticket and PRD, with open questions for the PM | PM | G3, G4 |
| 2 | Architecture | Components, interfaces, data model, diagram, proposed decisions | Tech lead | G2, G3 |
| 3 | Plan | 2–6 phases, each shippable on its own behind a flag | Tech lead + PM | G3, G4 |
| 4 | Test plan | Per phase: acceptance criteria in plain English (Given/When/Then), plus test cases | PM (criteria), QA (cases) | G4 |
| 5 | Build phase N | Tests first, then code, one PR per phase | Code review + CI | G2, G3 |
| 6 | Verify | Automated standards and security review, test results, coverage | CI green + reviewer | G2 |
| 7 | Demo | Short script and recording of the acceptance criteria running | PM | G4 |
| 8 | Close | Feature summary and accepted decisions added to the knowledge base | Automatic for the summary; owners for decisions | G1 |

## 6. Requirements

Priority: **M** must have for the pilot, **S** should have for the pilot, **L** later (org rollout).

### 6.1 Knowledge management (G1)

| ID | Requirement | P |
| --- | --- | --- |
| K1 | One git repo of plain markdown pages: org standards and rules; one page per service; decisions (ADRs); one entry per shipped feature; an index and a change log | M |
| K2 | Pages follow a defined schema (types, header fields, naming, links) that a machine can check | M |
| K3 | On every merged PR, an agent proposes updates to the affected service page and feature entry, and proposes new decisions | M |
| K4 | Agents may merge service and feature page changes after automated checks; standards and decisions need their human owners' approval | M |
| K5 | PR text, tickets, pasted documents and diffs are untrusted input to the knowledge agent; injected instructions must not reach merged pages | M |
| K6 | A scheduled check reports broken links, contradictions, stale service pages (code changed, page didn't) and orphan pages | M |
| K7 | Every Claude Code session, in any repo, can read the knowledge base and starts with the relevant service page in context | M |
| K8 | Existing services get a first-draft page from a one-off automated pass over their code, not written from scratch | M |
| K9 | Lessons and gotchas found during a session are captured for the knowledge base | S |
| K10 | The page format is portable beyond Claude Code (KnowledgeX / Open Knowledge Format is a candidate) | L |

### 6.2 Enforcing standards (G2)

| ID | Requirement | P |
| --- | --- | --- |
| R1 | Standards (coding, security, deployment per environment, dos and don'ts) live in the knowledge base, owned and approved by named humans | M |
| R2 | The standards relevant to the language and service being changed are in Claude's context when it designs or writes code | M |
| R3 | Every PR gets an automated review against the standards and for security before human review, by an agent separate from the one that wrote the code | M |
| R4 | Forbidden actions are blocked outright, not just discouraged: deploy commands, force-push, production credentials, edits to protected paths | M |
| R5 | The feature pipeline's templates and project principles are generated from the standards, so specs and plans follow them by construction | S |
| R6 | Standards violations found in review are reported by team and rule, so owners see which rules are misunderstood | L |

### 6.3 Developer workflow (G3)

| ID | Requirement | P |
| --- | --- | --- |
| P1 | A developer starts a feature from a Jira ticket ID with one command; the spec is drafted from the ticket and linked documents | M |
| P2 | Each stage has one command, produces the artifact in §5, and can be re-run on its own (it reads only earlier artifacts plus the knowledge base) | M |
| P3 | At any point, one command (or the session start) tells the developer where the feature stands and what to do next | M |
| P4 | The same commands and behaviour in every repo and on every machine, installed and updated centrally with no developer setup | M |
| P5 | Build writes failing tests from the test plan before any code, and is done when they pass | M |
| P6 | Plain, consistent terminology across commands, artifacts and docs; a new joiner can follow the path from a one-page guide | M |
| P7 | The same capabilities run locally (stages 1–5) and in CI (stages 6–8) | M |
| P8 | Onboarding a repo or team takes under a day of platform effort and no per-team code | L |

### 6.4 PM collaboration (G4)

| ID | Requirement | P |
| --- | --- | --- |
| C1 | PMs review and approve artifacts as rendered pages in a PR, without developer tooling | M |
| C2 | Open questions in the spec are visible to the PM and answerable inline; answers flow into the next stage | M |
| C3 | A stage refuses to start until its artifact is approved by the designated role; editing a file locally must not count as approval | M |
| C4 | Acceptance criteria have stable IDs reused as automated test names, so the PM can see which criteria pass | M |
| C5 | PMs are notified when something needs their review, and see everything waiting on them in one list | M |
| C6 | The demo is generated from the acceptance criteria on a preview environment, so the PM can accept without a meeting | S (depends on preview environments; Q4) |

### 6.5 Leadership visibility (G5)

| ID | Requirement | P |
| --- | --- | --- |
| V1 | One simple dashboard for leadership, PMs, tech leads and QA: a list of stories filterable by team, PM, stage, status and "waiting on me" | M |
| V2 | Each story shows: ticket and title, repo, stage and status, who it's waiting on and for how long, phase progress, and a direct link to the PR needing approval | M |
| V3 | Usable by people who don't use developer or ops tools | M |
| V4 | Status is derived from the artifacts and PRs, never entered by hand | M |
| V5 | Jira reflects stage changes, or at least links to the feature's artifacts | S |
| V6 | Adoption and cost per team: active users, sessions, spend, PRs with AI assistance | M |
| V7 | A weekly one-page summary: what shipped, what's stuck at which approval, cost by team | S |
| V8 | Cost per feature, with an alert on outliers | L |

### 6.6 Distribution, security and cost (all goals)

| ID | Requirement | P |
| --- | --- | --- |
| D1 | Engineers can't add unapproved extensions; everyone runs the same version | M |
| D2 | Changes to Bagheera are reviewed like production code, with security owning the parts that run on laptops | M |
| D3 | CI uses short-lived credentials; no long-lived keys in repos | M |
| D4 | CI spend is capped per job and reported separately from seat usage | M |

## 7. Success metrics

| Goal | Metric | Pilot target |
| --- | --- | --- |
| G1 | Pilot service pages updated within a day of a merge that changes them | ≥ 90% |
| G1 | Service pages verified by a human in the last 90 days | ≥ 80% |
| G2 | Merged PRs that went through the automated standards review | 100% in pilot repos |
| G2 | Standards issues caught before human review vs. in human review | Rising share caught automatically |
| G3 | Pilot engineers using the pipeline weekly | ≥ 6 of the pilot team |
| G3 | Features shipped end to end through all stages | 2 in the pilot |
| G4 | Median time from an artifact PR opening to the PM's first response | < 1 working day |
| G4 | PMs approve test plans without rewriting them | PM-written lines < 20% of the test plan |
| G4 | Status and clarification meetings per pilot team per week | Down vs. the pre-pilot baseline (count it in week 1) |
| G5 | Leadership can answer "where is feature X and what is it waiting on" without asking anyone | Yes, from the dashboard |
| All | CI cost per merged PR | Within a budget agreed before the pilot |

## 8. Constraints and assumptions

* **GitHub** for code, review and CI. **Jira** for tickets. **Anthropic API** for model access. Any design that depends on these must say so.
* Claude Code is the agent runtime, locally and in CI.
* Effort: 2 engineers, about 6–8 weeks to a pilot with one team of 6–8 engineers on a real feature; then rollout 2–3 teams at a time.
* Plugins, hooks and MCP servers run with the engineer's permissions on their machine; there is no sandbox.
* Build as little as possible. Prefer first-party and widely used open-source tools; anything built must say what was considered and why it didn't fit.
* **Preferred building blocks** (use unless there's a strong reason not to):
    * **GitHub Spec Kit** as the backbone of stages 1–4, customised through an org **preset** (our templates) and an org **extension** (the stages Spec Kit lacks, such as the test plan and the demo).
    * **DeepWiki-Open**, run once per repo, to draft the first service pages (K8); then the knowledge agent takes over. It's never a second source of truth.
    * **claude-code-action** for everything that runs in CI; one Claude Code **plugin**, distributed through managed settings, for everything on laptops.
* **Naming:** keep a light Jungle Book theme. The product is **Bagheera**; give themed names only to a few top-level things where a name helps people talk about them. Agents, commands, skills, files and workflows get plain descriptive names. Never use generic placeholder company names; the placeholder GitHub org is `seeonee`.

## 9. Rollout

1. **Foundations.** Knowledge base with standards written by their owners; first service pages drafted by DeepWiki; self-updating on merge; adoption and cost visibility switched on.
2. **Pilot, stages 1–4.** One team, one real feature; PMs approve in PRs; dashboard live.
3. **Pilot, stages 5–8.** Build, CI review, demo, close.
4. **Org rollout.** 2–3 teams at a time, each adding its service pages; the platform team owns Bagheera.

Expand only once the pilot team uses the approvals rather than skipping them.

## 10. Open questions

| # | Question | Blocks |
| --- | --- | --- |
| Q1 | Real GitHub org name, and where Bagheera's repos live | Foundations |
| Q2 | Do PMs and QA have GitHub accounts with review rights? | Approvals, dashboard |
| Q3 | Is the dashboard view-only, or should people approve, nudge or reassign from it? | Dashboard |
| Q4 | Do all services have per-PR preview environments? | Demo stage |
| Q5 | One org-wide knowledge base, or one per domain with shared standards? | Knowledge base |
| Q6 | Does Jira need to show the stage, or is a link from Jira enough? | Jira integration |
| Q7 | Claude plan (Team / Enterprise / API Console), which decides what built-in usage analytics exist | Adoption and cost visibility |
| Q8 | Data residency: Anthropic API, or Bedrock / Vertex? | Model access, DeepWiki setup |
| Q9 | Which team and feature pilot first, and what is that team's current meeting load? | Pilot, G4 baseline |

## Appendix A — Facts already checked (Oct 2026)

From the first design attempt. Re-verify before relying on them.

* **Claude Code hooks:** `SessionStart` can add context; `PreToolUse` can block a tool call; `SessionEnd` has a 1.5 s default budget (up to 60 s) and can't block; `Stop` fires after every turn.
* **Plugins:** subagents shipped in a plugin ignore the `hooks`, `mcpServers` and `permissionMode` fields; tool allowlists and `isolation: worktree` work. Managed settings can force-enable a plugin and restrict marketplaces (`extraKnownMarketplaces`, `enabledPlugins`, `strictKnownMarketplaces`).
* **Telemetry:** OpenTelemetry resource attributes are read once at launch; hooks can't change them; there's no git-branch attribute (repo identity via `OTEL_METRICS_INCLUDE_REPOSITORY`).
* **Built-in analytics:** claude.ai admin (Team/Enterprise) shows active users, sessions, PRs with Claude Code, a leaderboard and CSV export; a spend report gives per-user cost; the Enterprise Analytics API exposes it programmatically. The API Console dashboard shows usage and per-user spend.
* **claude-code-action:** supports `prompt`, `claude_args`, loading plugins from marketplaces, a custom GitHub App token, and OIDC workload-identity federation to the Anthropic API.
* **GitHub Spec Kit (v1.2):** commands are `/speckit-*` skills; customised via presets (templates, command overrides) and extensions (new commands); its `plan.md` is the technical plan and `tasks.md` the phased plan; `SPECIFY_FEATURE_DIRECTORY` sets the output folder, so `specs/<ticket-id>/` works without forking.
* **DeepWiki-Open (MIT, self-hosted):** no direct Anthropic provider (route through its bundled LiteLLM proxy, or Bedrock), and it needs an embedding model Anthropic doesn't offer (local Ollama `nomic-embed-text` works); exports Markdown via `POST /export/wiki`.
* **MCP for read-only repo access:** the official GitHub MCP server has a server-side read-only mode; `mcp-server-git` has no read-only mode and can't read or search file contents.
* **GitHub:** CODEOWNERS can require owner approval per path; "0 general approvals + code-owner review" still needs testing on a real repo.
* **KnowledgeX:** `kx check` / `kx review` validate OKF bundles (required fields, links, staleness, relations), but it treats `README.md`/`CLAUDE.md`/`CHANGELOG.md` as notes, writes files even on reads, and has no read-only or JSON output mode.

## Appendix B — Lessons from the first attempt

* Too many moving parts for a 2-person team: three repos, a self-hosted monitoring stack, a session-digest queue, six CI workflows and sixteen decisions at once. Spec Kit (preset + extension) and the one-off DeepWiki run were sound; keep them.
* Too many themed names: every agent and repo had a Jungle Book name. Keep the theme light and the terminology plain.
* The PM and leadership interface was an afterthought (Grafana). Design it for them from the start.
* Decisions must be explained in plain language and taken one at a time, each with a recommendation.
