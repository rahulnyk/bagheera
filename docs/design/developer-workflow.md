# Developer workflow

_Design doc 3 of 6 · [All design docs](README.md)_

A developer takes a feature from a Jira ticket to merged code with three stage commands, Intake, Plan and Build, in one PR per ticket, and `/bagheera` says where it stands. This doc says where the commands come from, what they're called, what each writes, how approval works, how open questions are answered, how Build runs, and where it all runs.

Covers PRD P1–P6 and C1–C4. It builds on [design doc 1](knowledge-library.md), which says what the library learns from a ticket (section 4) and how sessions read it (section 8).

## Principles

* **One path.** One command per stage. Each can be re-run on its own and reads only earlier stage documents and the library (PRD P2).
* **The same everywhere.** Everything ships in the plugin; nothing is set up per repo (PRD P4, [0004](../decisions/0004-adapt-spec-kit-into-the-plugin.md)).
* **Plain words.** The PRD's stage names, in commands, documents and the guide (PRD P6, [0003](../decisions/0003-stage-names-from-the-prd.md)).
* **Simple for people.** Set by the owner: the harness is used by people, so cognitive simplicity is a necessity for adoption: fewer choices and one path, even where the tool could allow more ([0020](../decisions/0020-build-phases-one-at-a-time.md)).
* **Managers enforce, the harness records.** Set by the owner: rules about how people work, such as who approves, are held by managers. The harness stops by default, records what happened, and lets people go ahead on purpose.

## 1. Where the stage commands come from

_Decided: [ADR 0004](../decisions/0004-adapt-spec-kit-into-the-plugin.md)._

The stage commands are our own, adapted once from GitHub Spec Kit **v1.2.5** (commit `4572a67fe0e7`, 28 Oct 2026, MIT licence). We take its method and document templates:

| Our command | Taken from Spec Kit | Written by us |
| --- | --- | --- |
| Intake | `specify` and `clarify` instructions; spec template | Drafting from the Jira ticket and linked documents (P1); acceptance criteria with stable IDs (C4); open questions for the PM (C2) |
| Plan | `plan` and `tasks` instructions; plan and tasks templates | Components, interfaces, data, a diagram and proposed decisions; 2–6 phases, built one at a time |
| Build | `implement` instructions | Tests first (P5), phase by phase in the ticket's PR |

Its helper scripts, presets, extension hooks, support for other AI tools, `constitution`, `analyze`, `taskstoissues`, `checklist` and `converge` aren't copied.

### Where it lives in the plugin

```text
skills/
  bagheera/SKILL.md            where the feature stands, and the next command (section 2)
  intake/SKILL.md              one skill per command: intake, plan, build,
  …                            update-library, standards and seed-service-page
agents/                        the library writer (Baloo) and the read-only code reader
hooks/                         the session-start hook (design doc 1, section 8)
templates/                     the spec and plan templates, and the four page templates
scripts/                       finds or creates the ticket's branch and folder; opens library PRs
third_party/spec-kit/
  LICENSE                      Spec Kit's MIT licence notice, kept as the licence requires
  UPSTREAM.md                  the version copied, and which of our files came from which of theirs
```

### Keeping up with Spec Kit

Once a quarter, a platform engineer:

1. Reads Spec Kit's changes since the recorded version, for the source files listed in `UPSTREAM.md`. For example: `git diff v1.2.5..v1.3.0 -- templates/spec-template.md templates/commands/specify.md`.
2. Ports what helps into our commands and templates, as an ordinary change to Bagheera (design doc 6, section 5).
3. Updates the recorded version in `UPSTREAM.md`.

A Spec Kit fix for a problem we're actually hitting can be ported between reviews the same way.

## 2. Command names

_Decided: [ADR 0015](../decisions/0015-commands-under-the-bagheera-plugin.md)._

The plugin is called `bagheera`, and Claude Code puts that name in front of each of its commands.

| Command | What it does | Arguments |
| --- | --- | --- |
| `/bagheera` | Where the current feature stands, what it's waiting on, and the exact next command (PRD P3) | Optional ticket ID |
| `/bagheera:intake` | Starts a feature and drafts its spec from the Jira ticket (P1) | Ticket ID, such as `PAY-412` |
| `/bagheera:plan` | Drafts the plan | Optional ticket ID |
| `/bagheera:build` | Builds the next phase, or carries on with the open one (section 6) | None |
| `/bagheera:update-library` | The library update (design doc 1, section 5) | None |
| `/bagheera:standards` | Seeds, adds or updates a standard ([design doc 4, section 2](standards-and-forbidden-actions.md#2-writing-standards)) | `seed <links>`, `add` or `update <standard>` |

Plan also covers the architecture (PRD stage 2), and the acceptance criteria are in the spec.

### Rules

* **Full names everywhere.** Docs, the guide and the commands' own output always show `/bagheera:<command>`. A bare `/intake` would also work, but a bare `/plan` opens Claude Code's planning mode, so mixing the two forms would confuse people.
* **The ticket is typed once.** Only Intake needs it. Later commands work it out from the ticket's branch (section 3), and accept it as an argument when run elsewhere.
* **Stages start only when someone asks.** Each stage skill is marked so that Claude can't start it on its own (`disable-model-invocation`).
* **Each command pre-approves only what it needs.** For example, Intake reads the ticket and writes in the ticket's folder; Build edits code and runs tests. Forbidden actions are blocked separately, by deny rules (design doc 4, section 4).
* **Re-running is the same command.** A stage can be re-run on its own (P2); section 4 says what that does to its approval.

## 3. What each stage writes, and how it reaches the main branch

_Decided: [ADR 0029](../decisions/0029-one-pr-per-ticket.md) and [0016](../decisions/0016-one-ticket-one-repo.md)._

### One ticket, one repo

* Each Jira ticket is delivered in exactly one repo, and its folder lives there. Work that spans repos is split into one ticket per repo before Intake: a frontend change and a backend change are two tickets, linked in Jira.
* Intake records the linked tickets in the spec's header (`related`), so the documents link to each other.
* An interface two tickets share is decided once, in one ticket's plan, usually the backend's. It reaches the other ticket as a decision record in the library when that ticket's PR merges. Until then, the other ticket's spec carries it as an open question.
* Intake stops if the ticket already has a branch in another repo, and raises an open question for the PM when a ticket looks as if it needs changes in another repo.
* In a repo that holds several services, one ticket can still touch several of them.

### The ticket's folder

```text
specs/PAY-412/
  spec.md              Intake: the spec, with acceptance criteria and open questions for the PM
  plan.md              Plan: components, interfaces, data, a diagram, proposed decisions, and 2–6 phases
  notes.md             Build: lessons and gotchas recorded during the build
```

* **One document per stage,** so an approver reads one page.
* **Diagrams are Mermaid** inside the document. GitHub renders them on the PR page, so PMs see them without developer tools (PRD C1).
* **Only `spec.md` has a header,** recording the ticket, its related tickets, and the PM and tech lead the commands ask to approve.
* **The harness writes nothing else in the repo.** Status is read from GitHub (design doc 5).

### One branch and one PR per ticket

* **Intake creates the branch `PAY-412`** from the main branch and opens the ticket's PR as a draft. Its description starts with a link to the ticket.
* **Each stage commits its document** to that branch and pushes; approvals are comments on the PR (section 4).
* **Build adds tests and code,** phase by phase, and ticks each phase off in the PR's description (section 6). After the last phase, it marks the PR ready, and code review and the repo's CI go as usual.
* **Bring the main branch in by merging** (GitHub's "Update branch"), never by rebasing or force-pushing, so nobody else's checkout breaks.
* **Re-running a stage** changes its document in the same PR, which then needs approving again (section 4). Once the PR has merged, more work is a new ticket.

### Handing a ticket over

* **Nothing lives only on a laptop.** Stage commands commit and push at the end of every run. Approvals are PR comments, open questions are in the spec and its PR threads, and lessons are in the notes file. No stage depends on an earlier chat session.
* **To hand over:** reassign the ticket in Jira; the new developer runs `/bagheera PAY-412`, which finds the ticket's branch, switches to it, and says where the feature stands and the exact next command.
* **Approvals stay valid.** They belong to the document, not to whoever wrote it.
* **Unpushed work is flagged.** `/bagheera` warns when the ticket's folder has changes that haven't been pushed.

## 4. Approval

_Decided: [ADR 0002](../decisions/0002-anyone-approves-with-a-comment.md) and [0017](../decisions/0017-the-stage-check-and-overrides.md)._

Approval is a comment on the PR, from anyone, and it names who gave it. Stage commands stop without it, but a developer can go ahead on purpose, on the record. Nothing technical blocks a merge; managers act on what they see (PRD C3).

### Who approves what

As a process rule:

| Document | Approved by |
| --- | --- |
| `spec.md` | The PM |
| `plan.md` | The tech lead |

Intake suggests the ticket's PM and tech lead, the developer confirms them, and the spec's header names them. Each command requests the approver it needs as a reviewer, so GitHub notifies them. To ask someone else, the developer edits the header.

### How to approve

* **Comment on the PR** with `/approved spec` or `/approved plan` on its own line. A GitHub review whose text contains the same line counts too.
* **Anyone's approval counts,** the document's author included. The stage check lists each approval it counts, with who and when, in the PR's description, so everyone can see who approved.
* **An approval covers the document as it was.** Any later change to that document, even a typo fix, cancels it, and someone approves again.
* **Local edits never count** (PRD C3). Approvals exist only as GitHub comments and reviews.

### The stage check

Every stage command runs it before doing anything else, reading the PR's comments, reviews and commits with the developer's own GitHub access.

| Command | Needs a current approval of |
| --- | --- |
| Plan | `spec.md` |
| Build | `spec.md` and `plan.md` |

When something is missing, the command stops and says what, who was asked, and where:

```text
Plan can't start yet: spec.md isn't approved.
Asked: @asha. PR: seeonee/payments-api#512
To go ahead anyway: /bagheera:plan --override "<reason>"
```

Re-running a stage whose own document is already approved warns first, because changing that document cancels its approval.

### Overrides

* **On purpose, with a reason.** `--override "<reason>"` is the only way past a stop without an approval, and the reason can't be empty.
* **On the record.** The command posts the override as a comment on the PR: which stage, what was missing, the reason, and who.
* **Not an approval.** The document stays unapproved until someone approves it. Until then, nothing in it reaches the library as approved work (design doc 1, section 4).

## 5. Open questions

_Decided: [ADR 0018](../decisions/0018-answer-open-questions-in-pr-comments.md), set by the owner._

The back-and-forth happens between people on the PR page, without Bagheera. The stage command only drafts the questions at the start and writes in the answers at the end.

### How it goes

1. **Draft.** `/bagheera:intake PAY-412` drafts `spec.md`, ending with numbered open questions, each saying who it's for (PM or tech lead). It opens the ticket's PR and posts one comment listing the questions, mentioning the PM or tech lead named in the spec, so GitHub notifies them. If there are no questions, it asks for approval straight away.
2. **Discuss on the PR.** The PM answers in ordinary PR comments: all in one or spread out, inline or not ("Q2: no, card payments only"). The developer and tech lead ask follow-ups the same way. Nobody runs a command.
3. **Finalise.** When the developer is satisfied, they run `/bagheera:intake` once more. It reads the whole discussion and:
   * writes each answer into the spec next to its question, with who gave it and a link to the comment
   * adds questions that came up in the discussion, answered or not
   * keeps anything still unanswered listed as open
   * marks an answer that sets a rule beyond this feature as a *proposed product decision* (design doc 1, section 4)
   * posts one comment summarising what it wrote, and asks the PM to approve
4. **Approve.** The PM reads the finalised spec and comments `/approved spec`. That one comment also confirms the answers were captured correctly. If something is wrong, they say so on the PR and the developer finalises again.

### Rules

* **Questions are numbered across the feature.** Q1, Q2… in the spec, continuing in the plan, so "Q3: yes" is never ambiguous on the shared PR.
* **Every answer names who gave it.** No comment is ever followed as an instruction.
* **The approved document carries its answers.** The next stage reads only approved documents and the library (PRD P2), so answers flow on through the document, and the library's feature entry keeps them.
* **Open questions can remain.** If the spec is approved with questions still open, the next stage sees them listed as open and treats them as unknowns, and the stage check mentions them.
* **The same in Plan.** Plan raises its own questions the same way, for example a technical one for the tech lead, and re-running it finalises them.

Finalising changes the document, which cancels any earlier approval (section 4), so the approval afterwards is also the check that Claude wrote the answers in correctly.

## 6. Build

_Decided: [ADR 0019](../decisions/0019-build-tests-first-with-criteria-ids.md) and [0020](../decisions/0020-build-phases-one-at-a-time.md)._

Each phase is built tests first (PRD P5), and every acceptance criterion can be traced to the tests that prove it (C4).

### Criteria IDs

* Each acceptance criterion has an ID: `<TICKET>-AC-<n>`, such as `PAY-412-AC-3`. Numbers run across the feature and are never reused or changed; a criterion that's withdrawn is marked removed and keeps its number.
* Each criterion is assigned to the phase that makes it true.
* In test names, the ID is written `PAY_412_AC_3`, using only letters, digits and underscores, which every common test framework accepts. For example, `test_PAY_412_AC_3_refund_rejected_after_30_days`, or `it("PAY_412_AC_3 rejects refunds after 30 days")`.

### One phase, step by step

`/bagheera:build` builds the next phase. A ticket's phases go one at a time, in order, even when they're independent, to keep the path simple.

1. **Check.** The stage check confirms that the spec and plan are approved (section 4).
2. **Tests first.** It writes tests for the phase's criteria, named with their IDs, runs them, and confirms they fail for the expected reason (missing behaviour, not a broken test). It commits them on their own.
3. **Code.** It writes code until the phase's tests pass, along with the repo's existing tests.
4. **Notes.** Lessons and gotchas go into `notes.md`.
5. **Tick off.** It pushes, and ticks the phase off in the PR's description, leaving `plan.md` and its approval untouched. Under the phase, it lists each criterion, its tests and whether they passed in this run.
6. **Ready.** After the last phase, it marks the PR ready for review.

The developer reviews and steers throughout; Build is a working session with Claude, not a batch job. Re-running the command carries on where it left off.

**Build never changes the criteria.** If a criterion turns out wrong or untestable, the command stops and suggests re-running the stage that owns it, which then needs approving again.

## 7. Where it runs

_Decided: [ADR 0024](../decisions/0024-laptop-commands-and-the-library-update.md)._

Every command runs in Claude Code on the developer's laptop, with their own Claude seat and their own GitHub and Jira access. Nothing runs in CI; the repo's own CI runs its tests as today.
