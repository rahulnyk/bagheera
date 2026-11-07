# Developer workflow

_Status: approved by the owner; current with the decisions of 2026-11-04 · Design doc 3 of 6 · [All design docs](README.md)_

A developer takes a feature from a Jira ticket to a shipped, demoed change through eight stages (PRD §5), one command per stage, in one PR per ticket. This doc says where the stage commands come from, what they're called, what each stage writes, how approval works, how open questions are answered, how build and demo run, and what runs where. The first system has three of the stage commands; [the plan](../plan.md) says which, and when the rest arrive.

Covers PRD P1–P7, C1–C4 and C6. It builds on [design doc 1](knowledge-library.md), which says what the library learns from a ticket (section 4) and how sessions read it (section 8).

## Principles

* **One path.** One command per stage. Each can be re-run on its own and reads only earlier stage documents and the library (PRD P2).
* **The same everywhere.** Everything ships in the plugin; nothing is set up per repo (PRD P4, [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)).
* **Plain words.** The PRD's stage names, in commands, documents, the dashboard and the guide (PRD P6, [0003](../decisions/0003-stage-names-from-the-prd.md)).
* **Simple for people.** The harness is used by people, so cognitive simplicity is a necessity for adoption: fewer choices and one path, even where the tool could allow more (set by the owner, 2026-10-30; [0040](../decisions/0040-build-phases-one-at-a-time.md)).
* **Managers enforce, the harness records.** Rules about how people work, such as QA review, are enforced by managers, who also grant exceptions. The harness warns and records; it doesn't block (set by the owner, 2026-10-30; 0038).

## 1. Where the stage commands come from

_Decided: [ADR 0004](../decisions/0004-carry-spec-kit-in-the-plugin.md), following [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)._

The stage commands are our own, adapted once from GitHub Spec Kit **v1.2.5** (commit `4572a67fe0e7`, 28 Oct 2026, MIT licence). We take its method and document templates:

| Our stage | Taken from Spec Kit | Written by us |
| --- | --- | --- |
| 1 Intake | `specify` and `clarify` instructions; spec template | Drafting from the Jira ticket and linked documents (P1); open questions for the PM (C2) |
| 2 Architecture | `plan` instructions; plan template (research, data model, interfaces) | Diagram, proposed decisions (PRD §5) and a standards check ([design doc 4, section 6](standards-and-review.md#6-the-standards-check-in-the-architecture)) |
| 3 Plan | `tasks` instructions; tasks template | 2–6 phases, built one at a time |
| 4 Test plan | — | All of it: acceptance criteria with stable IDs (C4), test cases |
| 5 Build | `implement` instructions | Tests first (P5), phase by phase in the ticket's PR |
| 6 Verify | `analyze` instructions (consistency across the stage documents) | The standards and security review (design doc 4) |
| 7 Demo | — | All of it: a demo script from the criteria (section 7) |
| 8 Close | — | All of it (design doc 1, section 4) |

Its helper scripts, presets, extension hooks, support for other AI tools, `constitution`, `taskstoissues`, `checklist` and `converge` stay behind.

### Where it lives in the plugin

```text
skills/
  bagheera/SKILL.md            where the feature stands, and the next command (section 2)
  intake/SKILL.md              one skill per stage: intake, architecture, plan, test-plan,
  …                            build, verify, demo and close; and update-library,
                               update-dashboard, standards and seed-service-page
templates/                     the stage document templates
scripts/feature                finds or creates the ticket's branch and folder
third_party/spec-kit/
  LICENSE                      Spec Kit's MIT licence notice, kept as the licence requires
  UPSTREAM.md                  the version copied, and which of our files came from which of theirs
```

### Keeping up with Spec Kit

Once a quarter, a platform engineer:

1. Reads Spec Kit's changes since the recorded version, for the source files listed in `UPSTREAM.md`. For example: `git diff v1.2.5..v1.3.0 -- templates/spec-template.md templates/commands/specify.md`.
2. Ports what helps into our commands and templates, as an ordinary change to the harness (design doc 6, section 6).
3. Updates the recorded version in `UPSTREAM.md`.

A Spec Kit fix for a problem we're actually hitting can be ported between reviews the same way.

## 2. Command names

_Decided: [ADR 0029](../decisions/0029-stage-commands-under-the-bagheera-plugin.md), following [0003](../decisions/0003-stage-names-from-the-prd.md)._

The plugin is called `bagheera`, and Claude Code puts that name in front of each of its commands.

| Command | What it does | Arguments |
| --- | --- | --- |
| `/bagheera` | Where the current feature stands, what it's waiting on, and the exact next command (PRD P3) | Optional ticket ID |
| `/bagheera:intake` | Stage 1: starts a feature and drafts its spec from the Jira ticket (P1) | Ticket ID, such as `PAY-412` |
| `/bagheera:architecture` | Stage 2 | Optional ticket ID |
| `/bagheera:plan` | Stage 3 | Optional ticket ID |
| `/bagheera:test-plan` | Stage 4 | Optional ticket ID |
| `/bagheera:build` | Stage 5: builds the next phase, or carries on with the open one (section 6) | None |
| `/bagheera:verify` | Stage 6: the PR review ([design doc 4, section 4](standards-and-review.md#4-the-pr-review)) | Optional ticket ID |
| `/bagheera:demo` | Stage 7: prepares the demo script | Optional ticket ID |
| `/bagheera:close` | Stage 8: shows what Close is waiting for; Close itself runs in the library update | Optional ticket ID |
| `/bagheera:update-library` | The library update (design doc 1, section 5) | None |
| `/bagheera:update-dashboard` | The dashboard update (design doc 5) | None |
| `/bagheera:standards` | Seeds, adds or updates a standard ([design doc 4, section 2](standards-and-review.md#2-writing-standards)) | `seed <links>`, `add` or `update <standard>` |

In the first system, Plan also covers the architecture, and the criteria are in the spec ([the plan](../plan.md#the-delivery-path)).

### Rules

* **Full names everywhere.** Docs, the guide, the dashboard and the commands' own output always show `/bagheera:<stage>`. A bare `/intake` would also work, but a bare `/plan` opens Claude Code's planning mode and a bare `/verify` runs its built-in check, so mixing the two forms would confuse people.
* **The ticket is typed once.** Only Intake needs it. Later stages work it out from the ticket's branch (section 3), and accept it as an argument when run elsewhere.
* **Stages start only when someone asks.** Each stage skill is marked so that Claude can't start it on its own (`disable-model-invocation`).
* **Each stage pre-approves only what it needs.** For example, Intake reads the ticket and writes in the feature folder; Build edits code and runs tests. Forbidden actions are blocked separately, by deny rules (design doc 4, section 5).
* **Re-running is the same command.** A stage can be re-run on its own (P2); section 4 says what that does to its approval.

## 3. What each stage writes, and how it reaches the main branch

_Decided: [ADR 0075](../decisions/0075-one-pr-per-ticket.md) for the PR, and [0031](../decisions/0031-one-ticket-one-repo.md) for one ticket per repo._

### One ticket, one repo

* Each Jira ticket is delivered in exactly one repo, and its folder lives there. Work that spans repos is split into one ticket per repo before Intake: a frontend change and a backend change are two tickets, linked in Jira.
* Intake records the linked tickets in the spec's header (`related`), so the documents link to each other.
* An interface two tickets share is decided once, in one ticket's architecture, usually the backend's. It reaches the other ticket as a decision record in the library when that ticket's PR merges. Until then, the other ticket's spec carries it as an open question.
* Intake stops if the ticket already has a branch in another repo, and raises an open question for the PM when a ticket looks as if it needs changes in another repo.
* In a repo that holds several services, one ticket can still touch several of them.

### The feature folder

```text
specs/PAY-412/
  spec.md              Intake: the spec, with open questions for the PM
  architecture.md      Architecture: components, interfaces, data model, a diagram, proposed decisions, a standards check
  contracts/           Architecture, only when the feature defines machine-readable interfaces (OpenAPI, protobuf)
  plan.md              Plan: 2–6 phases, built one at a time
  test-plan.md         Test plan: acceptance criteria with stable IDs, and test cases, per phase
  notes.md             Build: lessons and gotchas recorded during the build
```

* **One document per stage,** so an approver reads one page.
* **Diagrams are Mermaid** inside the document. GitHub renders them on the PR page, so PMs see them without developer tools (PRD C1).
* **Only `spec.md` has a header,** recording the ticket, its related tickets, and the PM and tech lead named to approve it ([0002](../decisions/0002-soft-approval-by-comment.md)).
* **The harness writes nothing else in the repo.** Status is worked out from GitHub (design doc 5).

### One branch and one PR per ticket

* **Intake creates the branch `PAY-412`** from the main branch and opens the ticket's PR as a draft. Its description starts with a link to the ticket.
* **Each stage commits its document** to that branch and pushes; approvals are comments on the PR (section 4).
* **Build adds tests and code,** phase by phase, and ticks each phase off in the PR's description (section 6). After the last phase, it marks the PR ready, and code review and the repo's CI go as usual.
* **Bring the main branch in by merging** (GitHub's "Update branch"), never by rebasing or force-pushing, so nobody else's checkout breaks.
* **Re-running a stage** replaces its document in the same PR, which then needs its approval again (section 4). Once the PR has merged, more work is a new ticket.

### Handing a ticket over

* **Nothing lives only on a laptop.** Stage commands commit and push at the end of every run. Approvals are PR comments, open questions are in the spec and its PR threads, and lessons are in the notes file. No stage depends on an earlier chat session, because each reads only earlier documents and the library (PRD P2).
* **To hand over:** reassign the ticket in Jira; the new developer runs `/bagheera PAY-412`, which finds the ticket's branch, switches to it, and says where the feature stands and the exact next command.
* **Approvals stay valid.** They belong to the document, not to whoever wrote it.
* **Unpushed work is flagged.** `/bagheera` warns when the feature folder has changes that haven't been pushed.

## 4. Approval

_Decided: [ADR 0032](../decisions/0032-stage-check-stops-and-records-overrides.md), [0002](../decisions/0002-soft-approval-by-comment.md) and [0032](../decisions/0032-stage-check-stops-and-records-overrides.md), following [0002](../decisions/0002-soft-approval-by-comment.md)._

Approval is a comment from the named person. Stage commands stop without it, but a developer can go ahead on purpose, on the record. Nothing technical blocks a merge; managers act on what they see (PRD C3).

### Who approves what

| Document | Approved by |
| --- | --- |
| `spec.md` | The PM named in the spec |
| `architecture.md` | The tech lead named in the spec |
| `plan.md` | The tech lead and the PM |
| `test-plan.md` | The PM for the acceptance criteria; QA for the test cases, encouraged but not required by the tool |
| The demo (section 7) | The PM |

In the first system, the PM approves the spec with its criteria, and the tech lead approves the plan with its architecture.

### How to approve

* **Comment on the PR** with `/approved <document>` on its own line: `/approved spec`, `/approved architecture`, `/approved plan`, `/approved test-plan`, `/approved demo`. A GitHub review whose text contains the same line counts too.
* **Only the named person counts.** Intake suggests the PM and tech lead, and the developer confirms them. Each command requests its approver as a reviewer, so GitHub notifies them. To swap someone, the developer edits the header, and the new person approves.
* **An approval covers the document as it was.** Any later change to that document, even a typo fix, cancels it, and the approver comments again.
* **QA review is encouraged, not enforced by the tool** (0038). A missing QA approval of the test cases never stops a stage; the stage check warns.
* **Nobody approves their own work.** An approval from anyone who committed a change to that document doesn't count.
* **Local edits never count** (PRD C3). Approvals exist only as GitHub comments and reviews.

### The stage check

Every stage command runs it before doing anything else, reading the PR's comments, reviews and commits with the developer's own GitHub access.

| Stage | Needs a current approval of |
| --- | --- |
| Architecture | `spec.md` |
| Plan | `spec.md`, `architecture.md` |
| Test plan | `spec.md`, `architecture.md`, `plan.md` |
| Build | All four planning documents, with the PM's approval of the test plan's criteria. A missing QA approval only warns |
| Demo | The ticket's PR merged |

When something is missing, the command stops and says what, who it's waiting on, and where:

```text
Plan can't start yet: spec.md isn't approved.
Waiting on: @asha. PR: seeonee/payments-api#512
To go ahead anyway: /bagheera:plan --override "<reason>"
```

Re-running a stage whose own document is already approved warns first, because changing that document cancels its approval.

### Overrides

_Decided: [ADR 0032](../decisions/0032-stage-check-stops-and-records-overrides.md)._

* **On purpose, with a reason.** `--override "<reason>"` is the only way past a stop, and the reason can't be empty.
* **On the record.** The command posts the override as a comment on the PR: which stage, what was missing, the reason, and who.
* **Not an approval.** The document stays unapproved until the right person approves it. Until then, nothing in it reaches the library as approved work (design doc 1, section 4).

## 5. Open questions

_Decided: [ADR 0033](../decisions/0033-answer-open-questions-in-pr-comments.md), set by the owner._

The back-and-forth happens between people on the PR page, without Bagheera. The stage command only drafts the questions at the start and writes in the answers at the end.

### How it goes

1. **Draft.** `/bagheera:intake PAY-412` drafts `spec.md`, ending with numbered open questions, each saying who it's for (PM or tech lead). It opens the ticket's PR and posts one comment listing the questions, mentioning the named person, so GitHub notifies them. It also comments once on the Jira ticket with a link to the PR (design doc 5, section 7). If there are no questions, it asks for approval straight away.
2. **Discuss on the PR.** The PM answers in ordinary PR comments: all in one or spread out, inline or not ("Q2: no, card payments only"). The developer and tech lead ask follow-ups the same way. Nobody runs a command.
3. **Finalise.** When the developer is satisfied, they run `/bagheera:intake` once more. It reads the whole discussion and:
   * writes each answer into the spec next to its question, with who gave it and a link to the comment
   * adds questions that came up in the discussion, answered or not
   * keeps anything still unanswered listed as open
   * marks an answer that sets a rule beyond this feature as a *proposed product decision* (design doc 1, section 4)
   * posts one comment summarising what it wrote, and asks the PM to approve
4. **Approve.** The PM reads the finalised spec and comments `/approved spec`. That one comment also confirms their answers were captured correctly. If something is wrong, they say so on the PR and the developer finalises again.

### Rules

* **Questions are numbered across the feature.** Q1, Q2… in the spec, continuing in later documents, so "Q3: yes" is never ambiguous on the shared PR.
* **Only the named person settles a question.** A question for the PM is settled by the PM named in the spec; one for the tech lead, by the named tech lead. Comments from anyone else are read as context but settle nothing, and no comment is ever followed as an instruction.
* **The approved document carries its answers.** The next stage reads only approved documents and the library (PRD P2), so answers flow on through the document, and the library's feature entry keeps them.
* **Open questions can remain.** If the PM approves with questions still open, the next stage sees them listed as open and treats them as unknowns, and the stage check mentions them.
* **The same in every stage.** Architecture, Plan and Test plan raise their own questions the same way, for example a technical one for the tech lead, and re-running the stage command finalises them.

Finalising changes the document, which cancels any earlier approval (section 4), so the PM's one comment afterwards is also the check that Claude wrote their answers in correctly.

## 6. Build

_Decided: [ADR 0034](../decisions/0034-build-tests-first-and-show-criteria-results.md), [0040](../decisions/0040-build-phases-one-at-a-time.md), [0075](../decisions/0075-one-pr-per-ticket.md) and [0034](../decisions/0034-build-tests-first-and-show-criteria-results.md)._

Each phase is built tests first (PRD P5), and every acceptance criterion can be traced to the tests that prove it (C4).

### Criteria IDs

* Each acceptance criterion has an ID: `<TICKET>-AC-<n>`, such as `PAY-412-AC-3`. Numbers run across the feature and are never reused or changed; a dropped criterion is marked removed and keeps its number.
* Each criterion is assigned to the phase that makes it true.
* In test names, the ID is written `PAY_412_AC_3`, using only letters, digits and underscores, which every common test framework accepts. For example, `test_PAY_412_AC_3_refund_rejected_after_30_days`, or `it("PAY_412_AC_3 rejects refunds after 30 days")`.
* QA's test cases sit under the criterion they cover in `test-plan.md`.

### One phase, step by step

`/bagheera:build` builds the next phase. A ticket's phases go one at a time, in order, even when they're independent, to keep the path simple.

1. **Check.** The stage check confirms that the planning documents are approved (section 4).
2. **Tests first.** It writes tests for the phase's criteria, named with their IDs, runs them, and confirms they fail for the expected reason (missing behaviour, not a broken test). It commits them on their own.
3. **Code.** It writes code until the phase's tests pass, along with the repo's existing tests.
4. **Notes.** Lessons and gotchas go into `notes.md`.
5. **Tick off.** It pushes, and ticks the phase off in the PR's description, leaving `plan.md` and its approval untouched. Under the phase, it lists each criterion, its tests and whether they passed in this run.
6. **Ready.** After the last phase, it marks the PR ready for review.

The developer reviews and steers throughout; Build is a working session with Claude, not a batch job. Re-running the command carries on where it left off.

**Build never changes the criteria.** If a criterion turns out wrong or untestable, the command stops and suggests re-running the stage that owns it, which then needs approval again.

### Criteria coverage and results in CI

An addition ([the plan](../plan.md#4-later-additions-a-team-can-make)). On the ticket's PR, CI checks that every criterion has at least one test whose name carries its ID, and keeps one PR comment up to date with a table by criterion:

| Criterion | What it says | Tests | Result |
| --- | --- | --- | --- |
| `PAY-412-AC-3` | Refunds are rejected after 30 days | `test_PAY_412_AC_3_refund_rejected_after_30_days` | Pass |
| `PAY-412-AC-4` | The customer is told why | `test_PAY_412_AC_4_rejection_message` | Fail |

For it, each repo's CI saves test results in JUnit format, as an artifact named `test-results`. Almost every test runner can produce this, for example pytest's `--junitxml`, Maven's default reports, or a JUnit reporter for Jest or Go. Without it, the table shows which criteria have tests, but not whether they pass.

## 7. Demo

_Decided: ADR 0035, set by the owner, following 0011._

The demo happens off the repo. The developer runs it on the stage environment, live in a meeting or as a recording shared however they like. Bagheera prepares the script, and only the PM's acceptance is recorded on GitHub.

1. **When the ticket's PR has merged and reached stage,** the developer runs `/bagheera:demo`. Deploying to stage is the org's own CD, so the developer confirms the build is there.
2. **The script.** It writes a short demo script from the approved criteria: for each criterion, what to do on stage, with which data, and what the PM should see. It's shown in the session and saved outside the repo; nothing is committed.
3. **The demo.** The developer gives it live, or records it and shares the recording. Claude can help drive a browser for a recording if the developer wants.
4. **Acceptance.** The PM comments `/approved demo` on the ticket's PR. That comment is what Close waits for (design doc 1, section 4).

How feedback from a demo becomes more work is settled when Demo is added ([0075](../decisions/0075-one-pr-per-ticket.md)).

## 8. What runs where

_Decided: ADR 0037, [0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md) and [0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md)._

### On laptops

Claude Code with the plugin runs `/bagheera` and the stage commands, each with the developer's own GitHub and Jira access. `/bagheera:verify` also runs here as a preview before pushing. In the pilot, everything runs on laptops: the developer runs the library update after each merge, and Verify and the dashboard update run from their commands when they're added ([design doc 6, section 3](distribution-security-and-cost.md#3-where-the-jobs-run)).

### In CI, from rollout

| What | Runs where | Starts when |
| --- | --- | --- |
| **Verify**: the PR review (design doc 4) and criteria coverage (section 6) | A workflow in `seeonee/bagheera`, required by one org ruleset on every repo whose `bagheera` property is `on`. It loads the same plugin as laptops (PRD P7) | A PR is opened or updated |
| **The criteria results table** (section 6) | The dashboard sync, every 15 minutes (design doc 5) | Its schedule; it reads each open PR's latest `test-results` |
| **The library update, with Close** (design doc 1, section 5) | A scheduled workflow in the library repo | Its schedule |
| **Tests** | The repo's own CI, as today | As today |

A ruleset workflow runs only when a PR is opened or updated, so the two things that respond to other events, test results finishing and a PM's comment, ride on the scheduled jobs. Nothing goes in any product repo, and Verify changes in one place.

### Setting it up

* **Once, by the GitHub org owners:** define a repo property, `bagheera`, that the platform team can set; and create the org ruleset that, on repos where `bagheera` is `on`, requires the Verify workflow from `seeonee/bagheera` on the default branch.
* **Once, by the platform team:** allow `seeonee/bagheera`'s workflows to be used by the org's repos.
* **Onboarding a repo, by the platform team:** set its `bagheera` property to `on` and seed it (design doc 2).
