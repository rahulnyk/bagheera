# Developer workflow

_Status: approved by the owner, 2026-10-29; amended with the owner's answers, 2026-10-30 · Design doc 3 of 6 · [All design docs](README.md)_

_Team walkthrough deck: [developer-workflow-walkthrough.pptx](developer-workflow-walkthrough.pptx) ([PDF](developer-workflow-walkthrough.pdf))._

A developer takes a feature from a Jira ticket to a shipped, demoed change through eight stages (PRD §5), one command per stage. This doc says where the stage commands come from, what they're called, what each stage writes, how approval works, how build and demo run, and what runs locally and what runs in CI.

Covers PRD P1–P7, C1–C4 and C6. It builds on [design doc 1](knowledge-library.md), which says what each stage adds to the knowledge library (section 5) and what each stage reveals from it (section 9).

## Principles

* **One path.** One command per stage. Each can be re-run on its own and reads only earlier stage documents and the library (PRD P2).
* **The same everywhere.** Everything ships in the plugin; nothing is set up per repo (PRD P4, [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)).
* **Plain words.** The PRD's stage names, in commands, documents, the dashboard and the guide (PRD P6, [0003](../decisions/0003-stage-names-from-the-prd.md)).
* **Simple for people.** The harness is used by people, so cognitive simplicity is a necessity for adoption: fewer choices and one path, even where the tool could allow more (set by the owner, 2026-10-30; [0040](../decisions/0040-build-phases-one-at-a-time.md)).
* **Managers enforce, the harness records.** Rules about how people work, such as QA review and tests first, are enforced by managers, who also grant exceptions. The harness warns and records; it doesn't block (set by the owner, 2026-10-30; [0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)).

## 1. Where the stage commands come from

_Decided: [ADR 0028](../decisions/0028-adapt-spec-kit-once-and-review-it-quarterly.md), following [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)._

The stage commands are our own, adapted once from GitHub Spec Kit **v1.2.5** (commit `4572a67fe0e7`, 28 Oct 2026, MIT licence). We keep its method and document templates and leave its machinery behind.

### What we take, stage by stage

| Our stage | Taken from Spec Kit | Written by us |
| --- | --- | --- |
| 1 Intake | `specify` and `clarify` instructions; spec template | Drafting from the Jira ticket and linked documents (P1); open questions for the PM (C2) |
| 2 Architecture | `plan` instructions; plan template (research, data model, interfaces) | Diagram, proposed decisions (PRD §5) and a standards check ([design doc 4, section 6](standards-and-review.md#6-project-principles-from-the-standards)) |
| 3 Plan | `tasks` instructions; tasks template | 2–6 phases, each shippable on its own behind a flag |
| 4 Test plan | — | All of it: acceptance criteria with stable IDs (C4), test cases |
| 5 Build | `implement` instructions | Tests first (P5), one PR per phase |
| 6 Verify | `analyze` instructions (consistency across the stage documents) | The standards and security review (design doc 4) |
| 7 Demo | — | All of it: a demo script from the criteria (section 7) |
| 8 Close | — | All of it (design doc 1, section 5) |

### What we leave out

| Spec Kit part | Why |
| --- | --- |
| Helper scripts (bash, PowerShell, Python) | They mostly number features, resolve presets and support many AI tools. One short script of our own finds or creates the feature's folder (`specs/<TICKET>/`) and branch, and lists its stage documents. |
| Extension hooks (`.specify/extensions.yml`) and presets | We change the content directly instead |
| Support for other AI tools | We only run in Claude Code |
| `constitution` | Replaced by the standards check in the architecture, filled from the current standards (PRD R5; [design doc 4, section 6](standards-and-review.md#6-project-principles-from-the-standards)) |
| `taskstoissues` | Jira is link-only ([0006](../decisions/0006-jira-link-only.md)) |
| `checklist`, `converge` | Not needed for the PRD's stages; the quarterly review can bring them in later |

### Where it lives in the plugin

```text
skills/
  bagheera/SKILL.md            where the feature stands, and the next command (section 2)
  intake/SKILL.md              one skill per stage: intake, architecture, plan, test-plan,
  …                            build, verify, demo, close, and revise (section 8)
templates/                     the stage document templates
scripts/feature                finds or creates the feature folder and branch
third_party/spec-kit/
  LICENSE                      Spec Kit's MIT licence notice, kept as the licence requires
  UPSTREAM.md                  the version copied, and which of our files came from which of theirs
```

### Keeping up with Spec Kit

Once a quarter, a platform engineer:

1. Reads Spec Kit's changes since the recorded version, for the source files listed in `UPSTREAM.md`. For example: `git diff v1.2.5..v1.3.0 -- templates/spec-template.md templates/commands/specify.md`.
2. Ports what helps into our commands and templates, as an ordinary change to the harness (how harness changes are reviewed and released is design doc 6).
3. Updates the recorded version in `UPSTREAM.md`.

A Spec Kit fix for a problem we're actually hitting can be ported between reviews the same way.

### Note on the PRD

PRD §8 names Spec Kit "customised through an org preset … and an org extension". We use its content, not the installed software. The wording change is already listed for the owner in [the design index](README.md#prd-wording-to-update).

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
| `/bagheera:build` | Stage 5: builds the next phase, or carries on with the open one (phases go one at a time, section 6) | None |
| `/bagheera:verify` | Stage 6 | Optional ticket ID |
| `/bagheera:demo` | Stage 7: prepares the demo script | Optional ticket ID |
| `/bagheera:close` | Stage 8: runs automatically in CI; typed by hand, shows what Close is waiting for (section 9) | Optional ticket ID |
| `/bagheera:revise` | Another iteration after planning has merged, for example after demo feedback (section 8) | The feedback, in plain words |

Verify and Close mostly run in CI, through the same skills (section 9). The developer runs Demo (section 7).

### Rules

* **Full names everywhere.** Docs, the guide, the dashboard and the commands' own output always show `/bagheera:<stage>`. A bare `/intake` would also work, but a bare `/plan` opens Claude Code's planning mode and a bare `/verify` runs its built-in check, so mixing the two forms would confuse people.
* **The ticket is typed once.** Only Intake needs it. Later stages work it out from the feature's branch (section 3), and accept it as an argument when run elsewhere.
* **Stages start only when someone asks.** Each stage skill is marked so that Claude can't start it on its own (`disable-model-invocation`). A stage runs when a developer types it or a CI workflow calls it.
* **Each stage pre-approves only what it needs.** For example, Intake reads the ticket and writes in the feature folder; Build edits code and runs tests. Forbidden actions are blocked separately, by deny rules (design doc 4).
* **Re-running is the same command.** A stage can be re-run on its own (P2). What it overwrites, and how approval carries over, is sections 3 and 4.

## 3. What each stage writes, and how it reaches the main branch

_Decided: [ADR 0030](../decisions/0030-one-planning-pr-per-feature.md) for PRs and branches, and [ADR 0031](../decisions/0031-one-ticket-one-repo.md) for one ticket per repo._

### One ticket, one repo

* Each Jira ticket is delivered in exactly one repo, and its folder lives there. Work that spans repos is split into one ticket per repo before Intake: a frontend change and a backend change are two tickets, linked in Jira.
* Intake records the linked tickets in the spec's header (`related`), so the documents link to each other, and so do the library's feature entries (design doc 1, section 4).
* An interface two tickets share is decided once, in one ticket's architecture, usually the backend's. It reaches the other ticket as a decision record in the library when that ticket's planning PR merges. Until then, the other ticket's spec carries it as an open question.
* The harness holds to the rule in three places:
  * Intake stops if the ticket already has a planning PR in another repo. It searches the org's PRs for the branch `<TICKET>/planning`.
  * The feature folder check (below) fails a ticket branch in a repo that doesn't hold that ticket's folder.
  * Intake raises an open question for the PM when a ticket looks as if it needs changes in another repo.
* In a repo that holds several services, one ticket can still touch several of them.

### The feature folder

```text
specs/PAY-412/
  spec.md              Intake: the spec, with open questions for the PM
  architecture.md      Architecture: components, interfaces, data model, a diagram, proposed decisions, a standards check
  contracts/           Architecture, only when the feature defines machine-readable interfaces (OpenAPI, protobuf)
  plan.md              Plan: 2–6 phases, each shippable on its own behind a flag
  test-plan.md         Test plan: acceptance criteria with stable IDs, and test cases, per phase
  phase-1-notes.md     Build: lessons and gotchas recorded during phase 1 (one file per phase)
```

* **One document per stage.** Spec Kit's separate research, data-model and quickstart files are folded into `architecture.md`, so an approver reads one page.
* **Diagrams are Mermaid** inside the document. GitHub renders them on the PR page, so PMs see them without developer tools (PRD C1).
* **Only `spec.md` has a header**, recording the ticket, its related tickets, and the story's named PM, tech lead and QA ([design doc 5, section 2](status-and-dashboard.md#2-the-status-model)). The folder and file names say the rest.
* **The harness writes nothing else in the repo.** Where status lives is design doc 5.
* **Close writes nothing here.** It builds the feature entry in the library (design doc 1, section 5).

Each document's sections come from its stage's template. Sections 5 and 6 cover the parts that need decisions: open questions and criteria IDs. The demo leaves nothing in the folder (section 7).

### PRs and branches

| Branch | PR | Writes | Merges when |
| --- | --- | --- | --- |
| `PAY-412/planning` | The planning PR, opened by Intake as a draft | `spec.md`, `architecture.md`, `contracts/`, `plan.md`, `test-plan.md` | All four documents are approved (section 4) |
| `PAY-412/phase-1`, `PAY-412/phase-2`, … | One PR per phase (PRD §5) | Code, tests and that phase's notes file | Code review and CI pass (sections 6 and 9) |
| `PAY-412/revision-1`, `PAY-412/revision-2`, … | A revision PR, after planning has merged (section 8) | The planning documents that change | Its approvals are in (section 8) |

* Every stage command ends by committing its document and pushing it to the PR.
* Re-running a stage on the planning branch replaces its document in the same PR. What that does to an earlier approval is section 4.
* A change after planning has merged goes through `/bagheera:revise`, in a revision PR of its own (`PAY-412/revision-1`) that touches only the documents that change (section 8).

### Rules that keep merges simple

1. **No two open PRs write the same file.** The planning PR writes only the feature's folder, each build phase writes only its own notes file there, and nothing shared, such as an index or a status file, is committed to a product repo.
2. **Always branch from the main branch, never from an unmerged branch.** A ticket's phases go one at a time: the next starts only after the previous one has merged ([0040](../decisions/0040-build-phases-one-at-a-time.md)). With no stacked PRs, a squash merge never forces a rebase.
3. **Bring the main branch in by merging** (GitHub's "Update branch"), never by rebasing or force-pushing. Because of rule 1, this never conflicts on the harness's files, and nobody else's checkout breaks.
4. **Branches are named after the ticket, not the person.**

**The feature folder check** runs in CI on every PR from a ticket branch. It fails if the ticket's folder isn't in this repo, if the PR writes in another ticket's folder, or if it writes a file in its own folder that its branch doesn't own (rule 1).

Two features changing the same code can still conflict in their build PRs. That is ordinary engineering, which the harness doesn't change; the rules only guarantee that the harness's own files never conflict.

### Handing a ticket over

* **Nothing lives only on a laptop.** Stage commands commit and push at the end of every run. Approvals are PR comments, open questions are in the spec and its PR threads, and lessons are in the notes files. No stage depends on an earlier chat session, because each reads only earlier documents and the library (PRD P2).
* **To hand over:**
  1. Reassign the ticket in Jira.
  2. The new developer runs `/bagheera PAY-412`. It finds the ticket's open branch, switches to it, and says where the feature stands and the exact next command.
  3. If they're mid-stage, they re-run that stage or keep editing on the same branch.
* **Approvals stay valid.** They belong to the document, not to whoever wrote it.
* **Unpushed work is flagged.** `/bagheera` warns when the feature folder has changes that haven't been pushed, so half a stage isn't left on one machine.

### What this means for the library

* The planning PR carries both the spec and the architecture, so product and technical decisions are proposed to the library when it merges: after the test plan is approved, and before Build (design doc 1, section 5).
* Feature entries record their one `repo` and their `related_tickets` (design doc 1, section 4).

## 4. Approval

_Decided: [ADR 0032](../decisions/0032-stage-check-stops-and-records-overrides.md), following [0002](../decisions/0002-soft-approval-by-comment.md)._

Approval is a comment from someone in the right role. Stage commands stop without it, but a developer can go ahead on purpose, and every time they do it is on record. Nothing technical blocks a merge; managers act on what the dashboard shows (PRD C3, soft enforcement as chosen in 0002).

### Who approves what

| Document | Approved by | Taken from `domains.yaml`, for the repo's domain |
| --- | --- | --- |
| `spec.md` | A PM | `product_owners` |
| `architecture.md` | A tech lead | `owners` |
| `plan.md` | A tech lead and a PM | `owners` and `product_owners` |
| `test-plan.md` | A PM for the acceptance criteria; QA for the test cases, encouraged but not required by the tool (below) | `product_owners` and `qa` |
| The demo, on the planning PR (section 7) | A PM | `product_owners` |

`domains.yaml` gains a `qa` entry per domain (design doc 1, section 4). Because each ticket lives in one repo, and each repo in one domain, there's never doubt about whose approval counts. In a minor revision, the PM's approval is optional (section 8).

### How to approve

* **Comment on the PR** with `/approved <document>` on its own line: `/approved spec`, `/approved architecture`, `/approved plan`, `/approved test-plan`, `/approved demo`. A GitHub review whose text contains the same line counts too, so a tech lead can approve the plan and the PR's merge in one step.
* **The commenter's role decides what it counts for.** On `test-plan.md`, a PM's approval covers the criteria and QA's covers the cases. On `plan.md`, one tech lead and one PM are needed.
* **An approval covers the document as it was** when the approval was given. If a later commit changes that document, its approval no longer counts, unless the change is a marked wording fix (below). Other documents keep theirs, but the stage check notes when a document changed after a later one was approved, so that later approver can take a look.
* **Wording fixes keep an approval** ([0039](../decisions/0039-wording-fixes-keep-an-approval.md)). A wording fix corrects spelling, grammar or formatting and changes no meaning. When a stage command makes one, Claude marks the commit with the trailer `Bagheera-Change: wording`; a person editing by hand adds the same trailer. The marked commits stay listed on the PR, so approvers can see them and object. Changing the named people in the spec's header doesn't cancel an approval either ([0050](../decisions/0050-named-people-per-story-team-fallback.md)).
* **QA review is encouraged, not enforced by the tool** ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)). A missing QA approval of the test cases never stops a stage. The stage check warns, and the gap is recorded on the dashboard and in the weekly summary; managers decide when it's needed and grant exceptions.
* **Nobody approves their own work.** An approval from anyone who committed a change to that document doesn't count.
* **Local edits never count** (PRD C3). Approvals exist only as GitHub comments and reviews.

### The stage check

Every stage command runs it before doing anything else. It reads the PR's comments, reviews and commits, and the role teams in `domains.yaml`, using the developer's own GitHub access.

| Stage | Needs a current approval of |
| --- | --- |
| Architecture | `spec.md` |
| Plan | `spec.md`, `architecture.md` |
| Test plan | `spec.md`, `architecture.md`, `plan.md` |
| Build | All four planning documents, with the PM's approval of the test plan's criteria, and the planning PR merged; the previous phase merged. After a revision: the tech lead's approvals, and the revision PR merged (section 8). A missing QA approval only warns. |
| Demo | Every phase merged (section 7) |

When something is missing, the command stops and says what, who it's waiting on, and where:

```text
Architecture can't start yet: spec.md isn't approved.
Waiting on: @seeonee/payments-pms. PR: seeonee/payments-api#512
To go ahead anyway: /bagheera:architecture --override "<reason>"
```

Re-running a stage whose own document is already approved warns first, because changing that document cancels its approval unless the change is wording only.

**Warnings, not stops.** Two gaps are warned about and recorded on the dashboard and in the weekly summary, but never stop anything: a missing QA approval of the test cases, and a phase PR without a tests-first commit (section 6). Managers act on them (0038).

### Overrides

* **On purpose, with a reason.** `--override "<reason>"` is the only way past a stop, and the reason can't be empty.
* **On the record.** The command posts the override as a comment on the PR: which stage, what was missing, the reason, and who. The dashboard and the weekly summary list every override (design doc 5).
* **Not an approval.** The document stays unapproved until the right person approves it, which clears the flag. Until then, nothing in it reaches the library as approved work: an unapproved architecture proposes no decisions, and the feature entry at Close leaves it out (design doc 1, section 5).
* **Merging the planning PR without every approval** isn't blocked, and counts as an override too.

### Note on the PRD

C3 says a stage "refuses to start until its artifact is approved". It does, by default; the wording change, "unless the developer overrides on purpose; every override is recorded and shown", is listed for the owner in [the design index](README.md#prd-wording-to-update).

## 5. Open questions

_Decided: [ADR 0033](../decisions/0033-answer-open-questions-in-pr-comments.md), set by the owner._

The back-and-forth happens between people on the PR page, without Bagheera. The stage command only drafts the questions at the start and writes in the answers at the end.

### How it goes

1. **Draft.** `/bagheera:intake PAY-412` drafts `spec.md`, ending with numbered open questions, each saying who it's for (PM or tech lead). It opens the planning PR and posts one comment listing the questions, mentioning the role's team so GitHub notifies them. If there are no questions, it asks for approval straight away.
2. **Discuss on the PR.** The PM answers in ordinary PR comments: all in one or spread out, inline or not ("Q2: no, card payments only"). The developer and tech lead ask follow-ups the same way. Nobody runs a command.
3. **Finalise.** When the developer is satisfied, they run `/bagheera:intake` once more. It reads the whole discussion and:
   * writes each answer into the spec next to its question, with who gave it and a link to the comment
   * adds questions that came up in the discussion, answered or not
   * keeps anything still unanswered listed as open
   * marks an answer that sets a rule beyond this feature as a *proposed product decision* (design doc 1, section 5)
   * posts one comment summarising what it wrote, and asks the PM to approve
4. **Approve.** The PM reads the finalised spec and comments `/approved spec`. That one comment also confirms their answers were captured correctly. If something is wrong, they say so on the PR and the developer finalises again.

### Rules

* **Questions are numbered across the feature.** Q1, Q2… in the spec, continuing in later documents, so "Q3: yes" is never ambiguous in the shared planning PR.
* **Only the right people settle a question.** A question for the PM is settled by someone in the domain's `product_owners`; one for the tech lead, by someone in `owners`. Comments from anyone else are read as context but settle nothing, and no comment is ever followed as an instruction (design doc 1, section 1).
* **The approved document carries its answers.** The next stage reads only approved documents and the library (PRD P2), so answers flow on through the document, and the library's feature entry keeps them.
* **Open questions can remain.** If the PM approves with questions still open, the next stage sees them listed as open and treats them as unknowns, and the stage check mentions them.
* **The same in every stage.** Architecture, Plan and Test plan raise their own questions the same way, for example a technical one for the tech lead, and re-running the stage command finalises them.

### Why the PM approves after finalising

Finalising changes the document, and an approval covers the document as it was (section 4). The PM's one comment afterwards is the check that Claude wrote their answers in correctly. It is also what turns those answers into approved work for the library.

## 6. Build

_Decided: [ADR 0034](../decisions/0034-build-tests-first-and-show-criteria-results.md)._

Each phase is built tests first in its own PR (PRD P5), and every acceptance criterion can be traced to the tests that prove it (C4).

### Criteria IDs

* The test plan gives each acceptance criterion an ID: `<TICKET>-AC-<n>`, such as `PAY-412-AC-3`. Numbers run across the feature and are never reused or changed; a dropped criterion is marked removed and keeps its number.
* Each criterion is assigned to the phase that makes it true.
* In test names, the ID is written `PAY_412_AC_3`, using only letters, digits and underscores, which every common test framework accepts. For example, `test_PAY_412_AC_3_refund_rejected_after_30_days`, or `it("PAY_412_AC_3 rejects refunds after 30 days")`.
* QA's test cases sit under the criterion they cover in `test-plan.md`.

### One phase, step by step

`/bagheera:build` builds the next phase. A ticket's phases go one at a time, in order, even when they're independent, to keep the path simple ([0040](../decisions/0040-build-phases-one-at-a-time.md)).

1. **Check.** The stage check confirms that the planning documents are approved, the planning PR has merged and the previous phase has merged (section 4).
2. **Branch.** It creates `PAY-412/phase-2` from the main branch.
3. **Tests first.** It writes tests for the phase's criteria from `test-plan.md`, named with their IDs, runs them, and confirms they fail for the expected reason (missing behaviour, not a broken test). It commits them on their own, marked with the trailer `Bagheera-Step: tests-first`.
4. **Code.** It writes code until the phase's tests pass, along with the repo's existing tests. The phase's work sits behind the flag named in the plan, using the org's own flag system.
5. **Notes.** Lessons and gotchas go into `phase-2-notes.md` through the knowledge skill (design doc 1, section 5).
6. **PR.** It pushes and opens the phase PR, listing the phase's criteria in the description.

The developer reviews and steers throughout; Build is a working session with Claude, not a batch job. Re-running the command on the open phase branch carries on where it left off.

**Build never changes the test plan.** If a criterion turns out wrong or untestable, the command stops and suggests a revision PR for `test-plan.md`, which needs approval again (section 3).

### What CI checks on a phase PR

| Check | What it does | If it fails |
| --- | --- | --- |
| **Criteria coverage** | Every criterion assigned to this phase has at least one test whose name carries its ID | The check fails |
| **Tests first** | The PR's first commit is the tests-first commit | A warning on the PR, recorded on the dashboard and in the weekly summary. Managers enforce tests first ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)). |
| **Criteria results** | Reads the test results and keeps one PR comment up to date with a table by criterion | Shows "no result" for a criterion whose tests didn't run |

The results comment looks like this:

| Criterion | What it says | Tests | Result |
| --- | --- | --- | --- |
| `PAY-412-AC-3` | Refunds are rejected after 30 days | `test_PAY_412_AC_3_refund_rejected_after_30_days` | Pass |
| `PAY-412-AC-4` | The customer is told why | `test_PAY_412_AC_4_rejection_message` | Fail |

The dashboard shows the same counts for each feature (design doc 5). Where these checks run is section 8.

### What each repo needs

Its CI must save test results in JUnit format, as an artifact named `test-results`. Almost every test runner can produce this, for example pytest's `--junitxml`, Maven's default reports, or a JUnit reporter for Jest or Go. Onboarding a repo includes this one configuration change, which needs no per-team code (PRD P8). Without it, the table shows which criteria have tests, but not whether they pass.

## 7. Demo

_Decided: [ADR 0035](../decisions/0035-demo-run-by-the-developer.md), set by the owner, following [0011](../decisions/0011-stage-environment-for-demos.md)._

The demo happens off the repo. The developer runs it on the stage environment, live in a meeting or as a recording shared however they like. Bagheera prepares the script, and only the PM's acceptance is recorded on GitHub.

### How it goes

1. **When every phase is on stage**, the developer runs `/bagheera:demo`. The stage check confirms that every phase has merged. Deploying to stage is the org's own CD, so the developer confirms the build is there.
2. **The script.** It writes a short demo script from the approved criteria: for each criterion, what to do on stage, with which data, and what the PM should see. It's shown in the session and saved outside the repo; nothing is committed or pushed.
3. **The demo.** The developer gives it live in a meeting, or records it and shares the recording however they choose. Claude can help drive a browser for a recording if the developer wants, but nothing requires it.
4. **Acceptance.** The PM comments `/approved demo` on the feature's planning PR, which is the feature's home on GitHub even after it has merged. That comment is what Close waits for (section 8).

If the demo turns up work that needs more development, the developer starts another iteration with `/bagheera:revise` (section 8).

### Note on the PRD

PRD §5 asks for a "recording", C6 for acceptance "without a meeting", and P7 for stages 6–8 in CI. Here the recording is optional, a meeting is fine, and the demo runs on the developer's machine. The wording changes are listed for the owner in [the design index](README.md#prd-wording-to-update).

## 8. Another iteration

_Decided: [ADR 0036](../decisions/0036-revise-with-one-command-minor-or-new-ticket.md), with the minor-revision and new-ticket rules set by the owner._

When a demo, or anything else after planning has merged, turns up more work, the developer starts another iteration with one command.

### How it goes

1. **Describe the feedback.** `/bagheera:revise "PM wants the refund reason in the email too"`, or paste the meeting notes.
2. **Minor or major.** Claude reads the feedback next to the approved documents, says whether it's minor or major and why, and the developer confirms.
   * **Minor:** it stays within the ticket's approved scope. For example, a criterion clarified or tightened, a small change to wording or behaviour, or a fix where the build didn't meet a criterion as intended.
   * **Major:** a new capability, criteria the spec's goals don't cover, or a change to interfaces other tickets rely on. The command doesn't revise; it drafts a new, linked ticket for the developer and PM to create in Jira.
3. **One revision PR.** For a minor change, it works out which documents change (usually the test plan and the plan, sometimes the spec or the architecture), opens `PAY-412/revision-1` with the edits, and posts any questions as one comment, as Intake does (section 5).
4. **Approval.** The tech lead approves plan and architecture changes, as usual, and QA's review of new test cases is encouraged, as everywhere (section 4). **The PM is mentioned, but their approval is optional.** The stage check doesn't wait for it, and it doesn't count as an override.
5. **Build and demo again.** Once the revision merges, `/bagheera:build` builds the new phase and `/bagheera:demo` prepares the next demo. The PM's `/approved demo` after it also counts as their approval of every minor revision since the last demo.

### Rules

* **Numbering carries on.** New phases, criteria and questions continue from the last ones (phase 4, `PAY-412-AC-9`, Q7). Nothing approved is renumbered or silently rewritten.
* **One open revision PR per ticket at a time.** This keeps rule 1 of section 3: no two open PRs write the same file.
* **The PM can still object.** A PM's comment on a revision PR is read like any other, and the developer can wait for their approval if the PM asks.
* **The feature stays open until the PM approves a demo.** On the dashboard, a minor revision's PM-owned changes show as waiting for the demo, not as missing approvals.
* **The library stays approved-only.** A minor revision's PM-owned changes count as approved work once the PM approves the next demo, so the feature entry at Close never includes anything unapproved (design doc 1, section 5).

## 9. What runs locally and what runs in CI

_Decided: [ADR 0037](../decisions/0037-verify-as-an-org-ruleset-workflow.md)._

### On the developer's laptop

Claude Code with the plugin runs `/bagheera` and the commands for Intake, Architecture, Plan, Test plan, Build, Demo and revisions, each with the developer's own GitHub and Jira access. `/bagheera:verify` also runs here as a preview before pushing, giving the same findings CI will.

### In CI

| What | Runs where | Starts when |
| --- | --- | --- |
| **Verify**: the PR review (design doc 4), the feature folder check (section 3) and criteria coverage (section 6) | A workflow in `seeonee/bagheera`, required by one org ruleset on every repo whose `bagheera` property is `on`. It loads the same plugin as laptops (PRD P7). | A PR is opened or updated |
| **The criteria results table** (section 6) | The dashboard sync job in `seeonee/bagheera`, every 15 minutes (design doc 5) | Its schedule; it reads each open phase PR's latest `test-results` |
| **Close**: the feature entry (design doc 1, section 5) | The library's hourly curation run (design doc 1, section 6) | A planning PR has `/approved demo` and every phase has merged |
| **Tests** | The repo's own CI, as today | As today |

`/bagheera:close` is what the curation run uses. Typed by hand, it shows what Close is waiting for and what the feature entry will say.

### Why this split

* **Ruleset workflows run only when a PR is opened or updated.** So the two things that respond to other events, test results finishing and a PM's comment, ride on scheduled jobs that already exist. No new jobs.
* **Nothing goes in any product repo**, and Verify changes in one place.
* **Only the default branch is covered.** Ruleset workflows block direct pushes to the branches they cover, so the ruleset targets each repo's default branch, which takes changes only through PRs.

### Setting it up

* **Once, by the GitHub org owners** (Foundations phase, `docs/plan.md`):
  1. Define a repo property, `bagheera`, that the platform team can set.
  2. Create the org ruleset: on repos where `bagheera` is `on`, for the default branch, require the Verify workflow from `seeonee/bagheera`.
* **Once, by the platform team:** allow `seeonee/bagheera`'s workflows to be used by the org's repos (a setting on that repo).
* **Onboarding a repo, by the platform team:** set its `bagheera` property to `on`, add it to `domains.yaml` and seed it (design doc 2). The team adds the JUnit results setting to its CI (section 6).

**Fallback.** The Verify workflow can also be called from a one-line workflow file in a repo. If the org ruleset isn't ready, the pilot can start that way and switch to the ruleset later without rewriting anything.
