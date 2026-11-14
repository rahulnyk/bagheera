# Bagheera — Requirements

_Status: draft for review · Owner: RN · 2026-11-14_

## 1. Summary

Bagheera is a delivery harness on Claude Code for an engineering org of about 100 engineers. It starts where features wait longest: helping PMs write the product requirements. A PM drafts a PRD (the product requirements document for a feature) with Baloo, who answers from Council Rock, the org's knowledge wiki, and checks every claim against the code. From an approved PRD, any developer takes the work to merged code by one clear path. Every piece of merged work updates Council Rock, so the next PRD starts with more.

## 2. Problem

* **Requirements hold features up, not building.** PMs draft PRDs from scattered Jira and Confluence pages, without seeing how the product works today or which services a change touches.
* **Conflicts with the code are found late.** They surface during planning or building, and send the requirements back for clarification and approval again.
* **Approval chains don't get followed.** PM, then tech lead, then the PM again is too much to keep up.
* **PMs aren't at home in GitHub.** Tools built around pull requests leave them out.
* **Knowledge is scattered and goes stale.** Nobody can see how services depend on each other, and Claude starts every session knowing none of it.
* **Every developer works differently.** There's no shared path, and corner cases such as branches or redoing a step are left to each person.
* **Nobody sees where work stands without asking.**

## 3. Goals

| ID | Goal | Success looks like |
| --- | --- | --- |
| G1 | PMs write requirements that hold up | A PM drafts a PRD with Baloo from Council Rock, checked against the code, in hours, and it's approved once |
| G2 | Knowledge that updates itself | Council Rock explains the org's code as a whole, and every merged piece of work updates it |
| G3 | One clear path from requirements to merged code | Any developer, in any repo, takes approved work to merged code the same way, always knows the next step, and never untangles branches by hand |
| G4 | Standards applied everywhere | Standards are written once by their owners, are in context whenever Claude plans or writes code, and dangerous actions are blocked |
| G5 | Everyone works and sees status in one place | PMs, developers and leadership use one portal. Only developers need git, and nobody compiles a status report |

### Principles

* **Requirements first.** Most of Bagheera's help goes into getting the PRD right.
* **Check against the code while writing.** A conflict found while drafting costs one question.
* **Few approvals.** Each thing is approved once, by one person; after a change, only the change needs approving.
* **Only developers need git.**
* **Brevity is a requirement.** Every document, answer and question has a hard limit.
* **Simplest first.** The smallest useful system, then more parts as real use shows they're needed.

### Non-goals

* Deploying. Existing CD deploys on merge, and every production change keeps a human approval.
* Replacing Jira or GitHub.
* A custom agent runtime or multi-agent framework.
* Full autonomy. Building stays human-steered.
* Ops tools as the interface for PMs or leadership.

## 4. Users

| User | Needs | Works in |
| --- | --- | --- |
| Product manager | Draft PRDs that hold up; ask how things work today; answer questions fast; approve once; see what's waiting on them | The portal |
| Developer (~100) | One path from approved work to merge; context already loaded; no ceremony that doesn't pay for itself | Claude Code; GitHub; the portal |
| Tech lead | Trust that plans fit the code and the standards; review once | GitHub review; the portal |
| QA | See each acceptance criterion tied to its tests | The portal |
| Leadership | Where every feature stands, what it's waiting on, throughput and cost | The portal |
| Architecture council | Own the org's standards and approve every change to them | Council Rock |
| Platform team (2 engineers) | Build and run Bagheera; help teams adopt it | Bagheera's repo |

## 5. Requirements

Priority: **M** must have; **S** should have; **L** at org adoption, which needs org admins or agreement across teams.

### 5.1 Drafting the PRD (G1)

| ID | Requirement | P |
| --- | --- | --- |
| R1 | A PM starts a PRD from an idea in plain words. No ticket or repo needs choosing first | M |
| R2 | Baloo drafts the PRD with the PM, from the idea, the Jira and Confluence pages the PM points to, Council Rock, and the code | M |
| R3 | The PM can ask Baloo, in plain language: how the product works today, which services and teams a change touches, what was decided before and why, and which past features are related. Every answer cites the pages or code it came from | M |
| R4 | Every statement the PRD makes about today's behaviour is checked against Council Rock and the code while it's drafted. Conflicts come back to the PM as questions, with the evidence in plain words | M |
| R5 | The draft names the services, repos and teams the change touches, following dependencies in Council Rock, and the standards that apply | M |
| R6 | Open questions are numbered and addressed to one person, who can answer where they already work. The answers are written into the PRD with who gave them | M |
| R7 | Every PRD follows one template: fixed sections, a word cap, and acceptance criteria in plain Given/When/Then, each with a stable ID | M |
| R8 | The PRD is approved once, by the person who owns it, with one action. A later change clears the approval, and approving again shows only what changed | M |
| R9 | An approved PRD is split into pieces of work, each traced to the requirements and criteria it delivers, and each reflected in Jira | M |
| R10 | A PM can stop drafting and pick up later, and lose nothing | M |

### 5.2 Council Rock, the knowledge wiki (G2)

| ID | Requirement | P |
| --- | --- | --- |
| K1 | One wiki for the whole org. It distils the org's knowledge so the code can be understood as a whole: services and how they depend on each other, shipped features, product and technical decisions, and standards | M |
| K2 | Pages link into a graph that people and Claude can follow both ways: a service to what it depends on and what depends on it; a feature to its PRD, the services it changed and its decisions | M |
| K3 | Every kind of document, the wiki's and the work's alike, is a typed page with defined header fields, sections and a word cap, which a machine can check. Git history is the change log | M |
| K4 | A piece of work's documents, such as its requirements and its plan, are checked into the code repo it changes | M |
| K5 | Finishing a piece of work includes updating the Council Rock pages it affects. This is enforced: the work can't merge until it's done | M |
| K6 | A person reviews every Council Rock change before it merges. Agents never merge | M |
| K7 | Text from tickets, documents, PRs and code is data, never instructions. Planted instructions must not reach merged pages | M |
| K8 | When Council Rock and the code disagree, the code wins and the page is corrected. Readers can see how current a page is | M |
| K9 | Every Claude Code session, in any repo, starts with the relevant part of Council Rock in context, and can follow its links | M |
| K10 | Existing services get first-draft pages from one automated pass over their code | M |
| K11 | Lessons and gotchas found while working are captured in Council Rock | S |
| K12 | Changes merged without Bagheera, such as bug fixes and refactors, update Council Rock too | L |
| K13 | Pages can be read by tools other than Claude Code | L |

### 5.3 From requirements to merged code (G3)

| ID | Requirement | P |
| --- | --- | --- |
| P1 | A developer starts on an approved piece of work in one action. Its PRD, criteria, the relevant Council Rock pages and the standards that apply are already in context | M |
| P2 | Before code is written, a short technical plan, checked against the code and the standards, is visible to the PM and the tech lead | M |
| P3 | Tests are written from the acceptance criteria before code, named with the criteria IDs. A piece of work is done when they pass | M |
| P4 | At any point, the developer can see where the work stands and the next step | M |
| P5 | Before any step runs, a pre-flight check confirms the right repo; its default branch, whatever it's called; the right working branch, clean and up to date; working connections to the tools the step needs; and the step's inputs, ready and approved. It fixes what's safe, and reports anything else in one line each, with the fix | M |
| P6 | Any step can be redone, such as redrafting requirements or replanning, simply by doing it again. Redoing keeps finished work and IDs | M |
| P7 | Nothing lives only on one laptop. Anyone can pick the work up where it stopped | M |
| P8 | The same behaviour in every repo and on every machine, updated automatically | M |
| P9 | Plain, consistent terms everywhere, so a new joiner can follow the path from a one-page guide | M |
| P10 | The same steps run in CI, unchanged | L |

### 5.4 The portal (G5)

| ID | Requirement | P |
| --- | --- | --- |
| V1 | A web portal people sign in to with the org's own sign-in. PMs need no GitHub account, no git and no developer tools | M |
| V2 | Every step of the lifecycle can be run and followed from the portal | M |
| V3 | One list of all work: PRD, piece of work, repo, where it stands, who it's waiting on and for how long. Filterable by team, PM and "waiting on me" | M |
| V4 | For each piece of work: its PRD, its plan, progress, each acceptance criterion and whether its tests pass, and links to the code changes | M |
| V5 | Status comes from the work itself, never typed in | M |
| V6 | People are told when something starts waiting on them, where they already look, such as Jira or email | M |
| V7 | Every approval records who gave it and when. Editing a file never counts as an approval | M |
| V8 | The PM can accept finished work against its criteria, from a demo | S |
| V9 | Adoption and cost per repo and per feature, and a weekly summary of what shipped and what's stuck | L |

### 5.5 Standards (G4)

| ID | Requirement | P |
| --- | --- | --- |
| S1 | Org standards (coding, security, deployment per environment, dos and don'ts) live in Council Rock and are owned by the architecture council, which approves every change | M |
| S2 | Each rule can be referred to on its own and checked from a diff | M |
| S3 | The standards that apply are in context whenever Claude plans or writes code | M |
| S4 | Forbidden actions are blocked outright, not just discouraged: deploy commands, force-push, production credentials, and edits to protected paths | M |
| S5 | An agent separate from the author reviews every change against the standards and for security, before human review | S |
| S6 | Violations found are reported by team and rule, so owners see which rules are misunderstood | L |
| S7 | The blocks are locked centrally, so no developer can loosen them | L |

### 5.6 Brevity (all goals)

| ID | Requirement | P |
| --- | --- | --- |
| B1 | Every document type has a hard word cap, and every section its own limit | M |
| B2 | Every message Bagheera shows a person fits on one screen: what's wrong, what changed, the next step | M |
| B3 | Only questions that block are asked: at most 5 open at a time, one line each, the most blocking first | M |
| B4 | Documents link to detail rather than copying it. No logs or transcripts in documents | M |

### 5.7 Security and distribution

| ID | Requirement | P |
| --- | --- | --- |
| D1 | Changes to Bagheera are reviewed like production code. Security owns the parts that run on laptops and in the portal | M |
| D2 | The portal holds only the access it needs, and keeps its keys in a secret store. Nothing Baloo reads can make it act | M |
| D3 | Engineers can't add unapproved extensions, and everyone runs the same version | L |
| D4 | CI uses short-lived credentials, with spend capped and reported apart from seats | L |

## 6. Success metrics

The pilot proves the whole path works end to end. Efficiency is measured afterwards.

| Pilot target |
| --- |
| At least 10 pieces of work, from PRDs drafted with Baloo and approved by their PM, taken to merged code |
| Each of them updates Council Rock |

**After the pilot:** time from idea to approved PRD; PRDs changed after approval, and why; conflicts with the code found while drafting rather than later; PMs and developers using Bagheera each week; status and clarification meetings per team per week; Council Rock pages that are current; cost per merged PR.

## 7. Constraints

* **GitHub** for code and review, **Jira** for tickets, **Confluence** for existing documents, the **Anthropic API** for models. Any design that depends on these must say so.
* **Claude Code** is the agent runtime for developers. The org has a Claude Code plugin marketplace, governed by its own policies.
* **Nothing in the first release depends on CI.** CI depends on org settings and needs agreement from more stakeholders.
* **Org admins set up the portal once,** for sign-in, hosting, and access to GitHub and Jira. After that, a team adopts Bagheera on its own.
* **Effort:** 2 engineers, about 6–8 weeks to a pilot with one team of 6–8 engineers.
* **Build as little as possible.** Prefer configuring first-party and widely used open-source tools. Anything we build must say what was considered and why it didn't fit.
* Plugins, hooks and MCP servers run with the engineer's permissions on their machine.

### Names

A light Jungle Book theme: themed names only for a few top-level things. Everything else gets a plain, descriptive name. Never use generic placeholder company names; the placeholder GitHub org is `seeonee`.

| Name | What it is | Status |
| --- | --- | --- |
| **Bagheera** | The product | Approved |
| **Council Rock** | The org's knowledge wiki. In the story, it's where the pack meets and the Law is kept | Proposed |
| **Baloo** | The knowledge keeper. It answers questions from Council Rock, helps draft PRDs, and drafts Council Rock's updates. In the story, Baloo teaches the Law | Proposed |

## 8. Rollout

1. **First release:** the smallest system that meets the **M** requirements.
2. **Pilot:** one team, at least 10 pieces of work.
3. **Additions** a team can make alone, each when the pilot shows it's needed.
4. **More teams,** 2–3 at a time.
5. **Org adoption:** the **L** requirements, each when its signal shows.

## 9. Open questions

| # | Question | Blocks |
| --- | --- | --- |
| Q1 | The real GitHub org, and where Bagheera's repos live | First release |
| Q2 | Claude plan and model access: Anthropic API, Bedrock or Vertex | First release |
| Q3 | Who on the org side sets up the portal's sign-in, hosting and access | First release |
| Q4 | Which approvals remain beyond the PM's approval of the PRD and code review as today, and who gives each | First release |
| Q5 | Where approved PRDs are published for people who read them in Confluence today | First release |
| Q6 | Which team pilots, and with which PRDs | Pilot |
