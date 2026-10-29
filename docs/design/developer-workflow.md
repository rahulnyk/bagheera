# Developer workflow

_Status: in progress · Design doc 3 of 6 · [All design docs](README.md)_

A developer takes a feature from a Jira ticket to a shipped, demoed change through eight stages (PRD §5), one command per stage. This doc says where the stage commands come from, what they're called, what each stage writes, how approval works, how build and demo run, and what runs locally and what runs in CI.

Covers PRD P1–P7, C1–C4 and C6. It builds on [design doc 1](knowledge-library.md), which says what each stage adds to the knowledge library (section 5) and what each stage reveals from it (section 9).

## Principles

* **One path.** One command per stage. Each can be re-run on its own and reads only earlier stage documents and the library (PRD P2).
* **The same everywhere.** Everything ships in the plugin; nothing is set up per repo (PRD P4, [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)).
* **Plain words.** The PRD's stage names, in commands, documents, the dashboard and the guide (PRD P6, [0003](../decisions/0003-stage-names-from-the-prd.md)).

## 1. Where the stage commands come from

_Decided: [ADR 0028](../decisions/0028-adapt-spec-kit-once-and-review-it-quarterly.md), following [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md)._

The stage commands are our own, adapted once from GitHub Spec Kit **v1.2.5** (commit `4572a67fe0e7`, 28 Oct 2026, MIT licence). We keep its method and document templates and leave its machinery behind.

### What we take, stage by stage

| Our stage | Taken from Spec Kit | Written by us |
| --- | --- | --- |
| 1 Intake | `specify` and `clarify` instructions; spec template | Drafting from the Jira ticket and linked documents (P1); open questions for the PM (C2) |
| 2 Architecture | `plan` instructions; plan template (research, data model, interfaces) | Diagram and proposed decisions (PRD §5) |
| 3 Plan | `tasks` instructions; tasks template | 2–6 phases, each shippable on its own behind a flag |
| 4 Test plan | — | All of it: acceptance criteria with stable IDs (C4), test cases |
| 5 Build | `implement` instructions | Tests first (P5), one PR per phase |
| 6 Verify | `analyze` instructions (consistency across the stage documents) | The standards and security review (design doc 4) |
| 7 Demo | — | All of it (C6) |
| 8 Close | — | All of it (design doc 1, section 5) |

### What we leave out

| Spec Kit part | Why |
| --- | --- |
| Helper scripts (bash, PowerShell, Python) | They mostly number features, resolve presets and support many AI tools. One short script of our own finds or creates the feature's folder (`specs/<TICKET>/`) and branch, and lists its stage documents. |
| Extension hooks (`.specify/extensions.yml`) and presets | We change the content directly instead |
| Support for other AI tools | We only run in Claude Code |
| `constitution` | Becomes project principles generated from the standards (PRD R5, design doc 4) |
| `taskstoissues` | Jira is link-only ([0006](../decisions/0006-jira-link-only.md)) |
| `checklist`, `converge` | Not needed for the PRD's stages; the quarterly review can bring them in later |

### Where it lives in the plugin

```text
skills/
  bagheera/SKILL.md            where the feature stands, and the next command (section 2)
  intake/SKILL.md              one skill per stage: intake, architecture, plan, test-plan,
  …                            build, verify, demo, close
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
| `/bagheera:build` | Stage 5, for one phase | Phase number; with none, the next unbuilt phase |
| `/bagheera:verify` | Stage 6 | Optional ticket ID |
| `/bagheera:demo` | Stage 7 | Optional ticket ID |
| `/bagheera:close` | Stage 8 | Optional ticket ID |

Stages 6–8 mostly run in CI, through the same skills (section 8).

### Rules

* **Full names everywhere.** Docs, the guide, the dashboard and the commands' own output always show `/bagheera:<stage>`. A bare `/intake` would also work, but a bare `/plan` opens Claude Code's planning mode and a bare `/verify` runs its built-in check, so mixing the two forms would confuse people.
* **The ticket is typed once.** Only Intake needs it. Later stages work it out from the feature's branch (section 3), and accept it as an argument when run elsewhere.
* **Stages start only when someone asks.** Each stage skill is marked so that Claude can't start it on its own (`disable-model-invocation`). A stage runs when a developer types it or a CI workflow calls it.
* **Each stage pre-approves only what it needs.** For example, Intake reads the ticket and writes in the feature folder; Build edits code and runs tests. Forbidden actions are blocked separately, by deny rules (design doc 4).
* **Re-running is the same command.** A stage can be re-run on its own (P2). What it overwrites, and how approval carries over, is sections 3 and 4.

## Still to come

3. What each stage writes, and the feature folder layout
4. Approval: the `/approved` comment, the stage check, and recorded overrides
5. Open questions and how answers reach the next stage
6. Build: tests first, one PR per phase, criteria IDs as test names
7. Demo on the stage environment
8. What runs locally and what runs in CI
