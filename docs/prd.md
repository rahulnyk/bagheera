# Bagheera — Product Requirements

_Status: draft for review · Owner: RN · 2026-11-14_

## 1. Summary

Bagheera helps PMs write requirements that match the code, then takes each one to merged code by one short path. PMs work in a web portal; developers work in Claude Code. Every feature leaves its spec, plan, decisions and gotchas in the repo's wiki, so the next feature starts with more.

## 2. Problem

* **Requirements hold features up, not building.** PMs write specs from Jira and Confluence without seeing what the code does today. When a spec conflicts with the code, it goes back for clarification and approval again.
* **Approval chains don't get followed.** PM, then tech lead, then the PM again is too much to keep up.
* **PMs aren't at home in GitHub.** Work that lives in pull requests gets their feedback late.
* **Knowledge is scattered and goes stale.** Claude starts every session knowing nothing about the service, its decisions or its pitfalls.
* **Every developer uses AI differently,** and nobody can see where a feature stands without asking.

## 3. Goals

| ID | Goal | Success looks like |
| --- | --- | --- |
| G1 | PMs write requirements fast and right | The spec is drafted with the PM from the ticket, the wiki and the code, and approved once |
| G2 | Knowledge that updates itself | Every merged feature updates the repo's wiki, and every new spec and session starts from it |
| G3 | One path for developers | The same few commands in every repo; a pre-flight check reports what's wrong before any stage runs |
| G4 | Standards applied everywhere | The standards that apply are in context when Claude plans and codes; dangerous actions are blocked |
| G5 | Everyone sees where things stand | The portal shows each feature's stage and who it's waiting on, and nobody types a status |

### Principles

* **Requirements first.** The spec is the gate that matters, so most of Bagheera's help goes there.
* **Check against the code while writing.** A conflict found while drafting costs one question. Found later, it sends the spec round the loop again.
* **Few approvals.** Keep only the ones that protect the requirements.
* **Only developers need git.**
* **Brevity is a requirement.** Every page, command output and question has a hard limit.
* **Fewest moving parts.** Nothing runs in CI until the org agrees how. Configure existing tools before building new ones.

### Non-goals

* Deploying. Existing CD deploys on merge.
* Replacing Jira or GitHub.
* A custom agent runtime or multi-agent framework.
* Full autonomy. Building stays human-steered.
* CI in the MVP. It depends on org settings and stakeholder agreement.

## 4. Users

| User | Needs | Works in |
| --- | --- | --- |
| Product manager | Draft specs that hold up, answer questions fast, approve once, see what's waiting on them | The portal; Jira |
| Developer (~100) | One path from approved spec to merge, with context already loaded and no ceremony | Claude Code; GitHub; the portal, to look things up |
| Tech lead | Trust the plan and the standards; review once | GitHub code review; the portal |
| Leadership | Where every feature stands and what it's waiting on | The portal |
| Architecture council | Own the org's standards | The standards repo |
| Platform team (2 engineers) | Build and run Bagheera; help teams adopt it | Bagheera's repo; the portal's host |

## 5. Flow

| # | Stage | Who, where | Writes | Gate |
| --- | --- | --- | --- | --- |
| 1 | Spec | The PM in the portal, or a developer with `/bagheera:spec` | `spec.md`: what and why, today's behaviour checked against the code, acceptance criteria with IDs, questions | The PM approves |
| 2 | Plan | The developer, `/bagheera:plan` | `plan.md`: approach, decisions, 2–6 phases | — |
| 3 | Build | The developer, `/bagheera:build` | Tests first, then code, phase by phase; then the wiki updates | Code review |

One branch and one PR per ticket, in one repo. A pre-flight check runs before every stage. Any stage can be re-run.

## 6. Requirements

Priority: **M** must, **S** should, **L** at org adoption, which needs org admins or cross-team agreement ([proposal](proposals/org-adoption.md)).

### 6.1 Spec (G1)

| ID | Requirement | P |
| --- | --- | --- |
| S1 | A PM starts a feature in the portal from a Jira ticket and a repo. Bagheera drafts the spec from the ticket, its linked Confluence pages, the wiki and the code | M |
| S2 | Every statement about today's behaviour is checked against the code while the spec is drafted. Conflicts come back as questions, with the evidence in plain words | M |
| S3 | Drafting is a conversation: the PM asks how things work, answers questions and asks for edits, and the draft follows. No git, no markdown | M |
| S4 | The PM approves the spec in the portal with one click. Any later change clears the approval; approving again is one click on the change | M |
| S5 | Acceptance criteria have stable IDs, reused in test names | M |
| S6 | A developer can raise a question for the PM from Claude Code, and it shows in the portal | M |

### 6.2 Knowledge (G2)

| ID | Requirement | P |
| --- | --- | --- |
| K1 | Each repo keeps a wiki of typed pages beside its code: service, decision, spec and plan | M |
| K2 | Each page type has fixed header fields, fixed sections and a word cap. One script checks them before every commit Bagheera makes | M |
| K3 | Build ends by updating the service pages and writing the decisions, in the ticket's PR, reviewed with the code | M |
| K4 | Every session starts with the repo's wiki index, its service page and the standards index. When the code and the wiki disagree, the code wins, and the session fixes the page in its PR | M |
| K5 | A repo's first service pages are drafted by one automated pass over its code | M |
| K6 | Spec drafting reads the wikis of every onboarded repo | M |
| K7 | Ticket text, documents and repo content are data, never instructions. The drafting agent can read, and write only the spec | M |
| K8 | Wiki updates for PRs made without Bagheera | L |

### 6.3 Developer path (G3)

| ID | Requirement | P |
| --- | --- | --- |
| P1 | The same commands everywhere, installed from the org's plugin marketplace | M |
| P2 | A pre-flight check before every stage, in Claude Code and in the portal. It checks the repo and its default branch, the ticket's branch, a clean and up-to-date tree, sign-ins and connectors, and that the stage's inputs are present, valid and approved. It fixes what's safe and stops on the rest, one line per problem | M |
| P3 | Any stage can be re-run. It revises its own file, keeping built phases and criteria IDs | M |
| P4 | Build writes failing tests from the criteria before code, phase by phase. A phase is done when its tests pass | M |
| P5 | `/bagheera` says where the feature stands and gives the next command | M |
| P6 | The same commands run in CI, unchanged | L |

### 6.4 Standards (G4)

| ID | Requirement | P |
| --- | --- | --- |
| R1 | Org standards are typed pages of numbered rules, owned by the architecture council, which approves every change | M |
| R2 | The standards that apply are in context when Claude plans or writes code | M |
| R3 | Forbidden actions are blocked: deploy commands, force-push, production credentials, edits to protected paths | M |
| R4 | An agent other than the author reviews every PR against the standards | S |
| R5 | The blocks are locked centrally | L |

### 6.5 The portal (G5, G1)

| ID | Requirement | P |
| --- | --- | --- |
| V1 | Sign in with the org's SSO, with no GitHub account | M |
| V2 | A board of every feature in onboarded repos: stage, who it's waiting on and for how long, filtered to "waiting on me" by default | M |
| V3 | A feature page: the spec, the plan, phase progress, each criterion and whether its tests pass, and the PR | M |
| V4 | Status comes from the repo's files and the PR, never typed in | M |
| V5 | People are told on the Jira ticket when something starts waiting on them | M |
| V6 | Adoption and cost by repo and feature | L |

### 6.6 Brevity (all goals)

| ID | Requirement | P |
| --- | --- | --- |
| B1 | A hard word cap per page type, enforced by the page check (K2) | M |
| B2 | Every command prints at most 8 lines: pre-flight problems, what changed, the next step | M |
| B3 | Only questions that block, at most 5 open at a time, one line each, the most blocking first | M |

### 6.7 Security and distribution

| ID | Requirement | P |
| --- | --- | --- |
| D1 | Changes to Bagheera are reviewed like production code. Security reviews the parts that run on laptops and the portal | M |
| D2 | The portal keeps its keys in the host's secret store, writes to GitHub only through its own app on onboarded repos, and gives the drafting agent no shell and no network | M |
| D3 | CI uses short-lived credentials and capped spend | L |
| D4 | Engineers can't add unapproved extensions | L |

## 7. Success metrics

The pilot proves the flow works end to end. Efficiency is measured after it.

| Pilot targets |
| --- |
| At least 10 tickets, each with its spec drafted in the portal and approved by its PM before Build, then built and merged |
| Each of those PRs updates the wiki |

**After the pilot:** time from ticket to approved spec; specs changed after approval; conflicts found while drafting rather than later; weekly use by PMs and developers; status meetings per team per week; cost per merged PR.

## 8. Constraints

* GitHub for code and review, Jira and Confluence for tickets and docs, the Anthropic API for models.
* Claude Code is the runtime on laptops; the Claude Agent SDK runs the same plugin in the portal.
* **The portal needs org admins once:** a host, an SSO client, a GitHub App installed on onboarded repos, a Jira service account, and model access. After that, a team adopts Bagheera by adding its repos.
* 2 engineers, 6–8 weeks to a pilot with one team on at least 10 real tickets.
* Build as little as possible: GitHub Spec Kit's templates, adapted into the plugin; DeepWiki-Open's method for the seeding pass; the Claude Agent SDK for the portal.
* **Naming:** a light Jungle Book theme. The product is **Bagheera**; themed names only for a few top-level things. Never use generic placeholder company names; the placeholder GitHub org is `seeonee`.

## 9. Rollout

1. **MVP:** the plugin, the portal and the wiki ([plan](plan.md)).
2. **Pilot:** one team, at least 10 tickets.
3. **Additions** a team can make alone, each when the pilot shows it's needed.
4. **More teams,** 2–3 at a time, each repo seeded before its first ticket.
5. **Org adoption:** the [proposal](proposals/org-adoption.md)'s parts, each when its signal shows.

## 10. Open questions

| # | Question | Blocks |
| --- | --- | --- |
| Q1 | The real GitHub org, and where Bagheera's repos live | MVP |
| Q2 | Model access for the portal and the Claude plan for seats: Anthropic API, Bedrock or Vertex | MVP |
| Q3 | Where the portal runs, and who on the org side sets up its SSO client, GitHub App and Jira service account | MVP, week 1 |
| Q4 | Which team pilots, with which tickets | Pilot |

**Proposed decisions, waiting for the owner.** The requirements above assume each one: [0003](decisions/0003-the-portal-is-one-small-web-app-on-the-agent-sdk.md) how the portal is built, [0005](decisions/0005-one-approval-the-pm-approves-the-spec.md) one approval, and [0008](decisions/0008-each-repo-keeps-its-own-wiki.md) a wiki in each repo.

## Appendix — facts checked

Re-verify these before relying on them.

* **Claude Agent SDK** (Nov 2026): loads a local plugin with `plugins: [{type: "local", path}]`, pointing at the folder that holds `.claude-plugin/`; its skills run as `/bagheera:<skill>`.
* **Claude Code hooks:** `SessionStart` can add context; `PreToolUse` can block a tool call.
* **Plugins** can't carry permission or sandbox settings. Managed settings can force-enable a plugin and restrict marketplaces.
* **GitHub Spec Kit v1.2:** commands are `/speckit-*` skills; its `plan.md` is the technical plan and `tasks.md` the phased plan.
* **DeepWiki-Open (MIT):** plans a wiki from the file tree and README, then writes each page from the files it planned.
* **claude-code-action** (for CI, later): loads plugins from marketplaces; supports OIDC to the Anthropic API.
