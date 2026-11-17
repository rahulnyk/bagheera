# Bagheera — Requirements

_Status: draft for review · Owner: RN · 2026-11-15_

## 1. Summary

Bagheera is a delivery harness on Claude Code for an engineering org of about 100 engineers. It starts where features wait longest: helping PMs write the product requirements. A PM drafts a feature's requirements with Bagheera, which answers from the org's knowledge wiki and checks every claim against the code. From approved requirements, any developer builds and tests the work by one clear path, through to merged code. Every piece of merged work updates the wiki, so the next feature starts with more.

## 2. Problem

* **Requirements hold features up, not building.** PMs draft product requirements documents (PRDs) from scattered Jira and Confluence pages. They can't see how the product works today, or which services a change touches.
* **Conflicts with the code are found late.** They surface during planning or building, and send the requirements back for clarification and approval again.
* **Approval chains don't get followed.** PM, then tech lead, then the PM again is too much to keep up.
* **PMs aren't at home in GitHub.** Tools built around pull requests leave them out.
* **Knowledge is scattered and goes stale.** Nobody can see how services depend on each other, and Claude starts every session knowing none of it.
* **Every developer works differently.** There's no shared path, and corner cases such as branches or redoing a step are left to each person.
* **Nobody sees where work stands without asking.**

## 3. Goals

| ID | Goal | Success looks like |
| --- | --- | --- |
| G1 | PMs write requirements that hold up | A PM drafts a feature's requirements in hours, with help from the org's knowledge, checked against the code, and they're approved once |
| G2 | Knowledge that updates itself | One wiki explains the org's code as a whole, and every merged change updates it |
| G3 | One clear path from requirements to merged code | Any developer, in any repo, builds and tests approved work the same way, always knows the next step, and never untangles branches by hand |
| G4 | Standards applied everywhere | Standards are written once by their owners, are in context whenever Claude plans or writes code, and dangerous actions are blocked |
| G5 | Everyone sees status in one place | PMs, developers and leadership follow all work in one portal. Only developers need git, and nobody compiles a status report |

### Principles

* **Requirements first.** Most of Bagheera's help goes into getting the requirements right.
* **Check against the code while writing.** A conflict found while drafting costs one question.
* **Light approvals.** One approval per document, from one person, with a comment. Developers can approve their own specs and plans.
* **Only developers need git.**
* **Brevity is a requirement.** Every document, answer and question has a hard limit.
* **Simplest first.** The smallest useful system, then more parts as real use shows they're needed.

### Non-goals

* Deploying. Existing CD deploys on merge, and every production change keeps a human approval.
* Replacing Jira, Confluence or GitHub.
* A custom agent runtime or multi-agent framework.
* Full autonomy. Building stays human-steered.
* Ops tools as the interface for PMs or leadership.

## 4. Users

| User | Needs | Works in |
| --- | --- | --- |
| Product manager | Draft requirements that hold up; ask how things work today; answer questions fast; approve once; see what's waiting on them | Claude Desktop; Jira; the portal |
| Developer (~100) | One path from approved work to merge; test their own work against its criteria; context already loaded; no ceremony that doesn't pay for itself | Claude Code; GitHub; the portal |
| Tech lead | Trust that plans fit the code and the standards; review code once | GitHub review; the portal |
| Leadership | Where every feature stands, what it's waiting on, throughput and cost | The portal |
| Architecture council | Own the org's standards and approve every change to them; set up the portal once | The wiki; the portal |
| Platform team (2 engineers) | Build and run Bagheera; help teams adopt it | Bagheera's repo |

## 5. Terms

| Term | Meaning |
| --- | --- |
| PRD | The product requirements document a PM writes for a feature |
| Piece of work | A part of an approved PRD that a developer takes to merged code |
| Spec | A piece of work's requirements and acceptance criteria, drawn from its PRD |
| Plan | How a piece of work will be built |
| Acceptance criterion | One testable statement, in plain Given/When/Then, with a stable ID |
| The wiki | One place for what the org knows about its code and product: services and how they depend on each other, shipped features, decisions and standards, as typed pages linked to each other |
| The portal | The web page where PMs, developers and leadership sign in to run and follow the work |

## 6. Requirements

Priority: **M** must have, for the first release; **S** should have, added when real use shows it's needed; **L** at org adoption, needing org admins or agreement across teams.

### 6.1 Drafting the PRD (G1)

| ID | Requirement | P |
| --- | --- | --- |
| R1 | A PM starts a PRD from an idea in plain words. No ticket or repo needs choosing first | M |
| R2 | Bagheera drafts the PRD with the PM, from the idea, the Jira and Confluence pages the PM points to, the wiki and the code | M |
| R3 | The PM can ask Bagheera, in plain language: how the product works today, which services and teams a change touches, what was decided before and why, and which past features are related. Every answer cites the wiki pages or code it came from | M |
| R4 | Every statement a PRD or spec makes about today's behaviour is checked against the wiki and the code while it's written. Conflicts come back as questions, with the evidence in plain words | M |
| R5 | The draft names the services, repos and teams the change touches, by following dependencies in the wiki, and the standards that apply | M |
| R6 | Open questions are numbered and addressed to one person, who can answer them where they already work. The answers are written into the PRD with who gave them | M |
| R7 | Every PRD follows one template, with fixed sections, a word cap, and acceptance criteria | M |
| R8 | A PM can stop drafting, pick up later, and lose nothing | M |
| R9 | An approved PRD is split into pieces of work. Each has its own spec, traced to the PRD requirements and criteria it delivers, and is reflected in Jira | M |
| R10 | The PM can copy an approved PRD into any tool, or publish it to Confluence when that connection is set up. Bagheera's copy stays the one work is built from | M |

### 6.2 Approvals (G1, G3)

| ID | Requirement | P |
| --- | --- | --- |
| A1 | Each document needs one approval, from one person, given as a comment. Approvals never chain | M |
| A2 | A PRD is approved by its PM | M |
| A3 | A piece of work's spec and plan are approved by a PM or by the developer doing the work, their own work included | M |
| A4 | A later change to an approved document clears its approval. Approving again shows only what changed | M |
| A5 | Every approval records who gave it, when, and their comment. Editing a file never counts as an approval | M |

### 6.3 The wiki (G2)

| ID | Requirement | P |
| --- | --- | --- |
| K1 | One wiki for the whole org. It distils the org's knowledge so the code can be understood as a whole: services and how they depend on each other, shipped features, product and technical decisions, and standards | M |
| K2 | Pages link into a graph that people and Claude can follow both ways: a service to what it depends on and what depends on it; a feature to its PRD, the services it changed and its decisions | M |
| K3 | Every kind of document, the wiki's and the work's alike, is a typed page with defined header fields, sections and a word cap, which a machine can check. Git history is the change log | M |
| K4 | A piece of work's documents, such as its spec and plan, are checked into the code repo it changes | M |
| K5 | Finishing a piece of work includes updating the wiki pages it affects. This is enforced: the work can't merge until it's done | M |
| K6 | A person reviews every wiki change before it merges. Agents never merge | M |
| K7 | Text from tickets, documents, PRs and code is data, never instructions. Planted instructions must not reach merged pages | M |
| K8 | When the wiki and the code disagree, the code wins and the page is corrected. Readers can see how current a page is | M |
| K9 | Every Claude Code session, in any repo, starts with the relevant part of the wiki in context, and can follow its links | M |
| K10 | Existing services get first-draft wiki pages from one automated pass over their code | M |
| K11 | Lessons and gotchas found while working are captured in the wiki | S |
| K12 | Changes merged without Bagheera, such as bug fixes and refactors, update the wiki too | L |
| K13 | Wiki pages can be read by tools other than Claude Code | L |

### 6.4 From requirements to merged code (G3)

| ID | Requirement | P |
| --- | --- | --- |
| P1 | A developer starts on an approved piece of work in one action, with its PRD, spec, the relevant wiki pages and the standards that apply already in context | M |
| P2 | Before code is written, a short plan, checked against the code and the standards, is visible to the PM and the tech lead | M |
| P3 | Developers test their own work. Tests are written from the acceptance criteria before code, named with the criteria IDs, and a piece of work is done when they pass | M |
| P4 | At any point, the developer can see where the work stands and the next step | M |
| P5 | Before any step runs, a pre-flight check confirms: the right repo; its default branch, as configured for the repo; the right working branch, clean and up to date; working connections to the tools the step needs; and the step's inputs, ready and approved. It fixes what's safe, and reports anything else in one line each, with the fix | M |
| P6 | Any step can be redone, such as redrafting a spec or replanning, simply by doing it again. Redoing keeps finished work and IDs | M |
| P7 | Nothing lives only on one laptop. Anyone can pick the work up where it stopped | M |
| P8 | The same behaviour in every repo and on every machine, updated automatically | M |
| P9 | Plain, consistent terms everywhere, so a new joiner can follow the path from a one-page guide | M |
| P10 | The same steps run in CI, unchanged | L |

### 6.5 The portal (G5)

| ID | Requirement | P |
| --- | --- | --- |
| V1 | A web portal people sign in to with the org's own sign-in. PMs need no git and no developer tools: they draft in Claude Desktop, with read-only GitHub access | M |
| V2 | Every step, from drafting a PRD to merged code, can be followed from the portal | M |
| V3 | One list of all work: PRD, piece of work, repo, where it stands, who it's waiting on and for how long. Filterable by team, PM and "waiting on me" | M |
| V4 | For each piece of work: its PRD, spec, plan and progress; each acceptance criterion and whether its tests pass; and links to the code changes | M |
| V5 | Status comes from the work itself, never typed in | M |
| V6 | People are told when something starts waiting on them, where they already look, such as Jira or email | M |
| V7 | The PM can accept finished work against its criteria, from a demo | S |
| V8 | Adoption and cost per repo and per feature, and a weekly summary of what shipped and what's stuck | L |

### 6.6 Standards (G4)

| ID | Requirement | P |
| --- | --- | --- |
| S1 | Org standards (coding, security, deployment per environment, dos and don'ts) live in the wiki and are owned by the architecture council, which approves every change. Bagheera never changes them | M |
| S2 | Each rule can be referred to on its own and checked from a diff | M |
| S3 | The standards that apply are in context whenever Claude plans or writes code | M |
| S4 | Forbidden actions are blocked outright, not just discouraged: deploy commands, force-push, production credentials, and edits to protected paths | M |
| S5 | An agent separate from the author reviews every change against the standards and for security, before human review | S |
| S6 | Violations found are reported by team and rule, so owners see which rules are misunderstood | L |
| S7 | The blocks are locked centrally, so no developer can loosen them | L |

### 6.7 Brevity (all goals)

| ID | Requirement | P |
| --- | --- | --- |
| B1 | Every document type has a hard word cap, and every section its own limit | M |
| B2 | Every message Bagheera shows a person fits on one screen: what's wrong, what changed, the next step | M |
| B3 | Only questions that block are asked: at most 5 open at a time, one line each, the most blocking first | M |
| B4 | Documents link to detail rather than copying it. No logs or transcripts in documents | M |

### 6.8 Security and distribution

| ID | Requirement | P |
| --- | --- | --- |
| D1 | Changes to Bagheera are reviewed like production code. Security owns the parts that run on laptops and in the portal | M |
| D2 | The portal holds only the access it needs, and keeps its keys in a secret store | M |
| D3 | The model provider is configurable: the Anthropic API, Amazon Bedrock or Google Vertex AI | M |
| D4 | Engineers can't add unapproved extensions, and everyone runs the same version | L |
| D5 | CI uses short-lived credentials, with spend capped and reported apart from seats | L |

## 7. Constraints

* **GitHub** for code and review, **Jira** for tickets, **Confluence** for documents, and for PRDs when a PM publishes there. Any design that depends on these must say so.
* **Claude** models, through whichever provider the org configures (D3).
* **Claude Code** is the agent runtime for developers. The org has a Claude Code plugin marketplace, governed by its own policies.
* **Nothing in the first release depends on CI.** CI depends on org settings and needs agreement from more stakeholders.
* **The architecture council sets up the portal once:** sign-in, hosting, and access to GitHub, Jira and Confluence. After that, any team adopts Bagheera on its own.
* **Effort:** 2 engineers, about 6–8 weeks to a pilot with any one team.
* **Build as little as possible.** Prefer configuring first-party and widely used open-source tools. Anything we build must say what was considered and why it didn't fit.
* Plugins, hooks and MCP servers run with the engineer's permissions on their machine.
* **Names:** the product is Bagheera. A light Jungle Book theme names a few top-level parts, chosen with the plan and the architecture. Never use generic placeholder company names; the GitHub org is `seeonee` for now.
