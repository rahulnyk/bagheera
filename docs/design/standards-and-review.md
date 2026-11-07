# Standards and review

_Status: approved by the owner; current with the decisions of 2026-11-04 · Design doc 4 of 6 · [All design docs](README.md)_

Standards are written once by their owners and applied everywhere: in Claude's context when it designs and writes code, checked on every PR, with dangerous actions blocked outright (PRD G2). This doc says how rules are named, how standards are written and changed, how sessions see the relevant ones, what the PR review checks, how forbidden actions are blocked, and how designs check themselves against the standards. What the first system includes, and when the rest arrives, is in [the plan](../plan.md).

Covers PRD R1–R6. It builds on [design doc 1](knowledge-library.md), which set the page format, approvals and the format check, and on [design doc 3](developer-workflow.md), which set where Verify runs (section 8).

## Principles

* **Written once, by their owners.** Standards are approved pages in the library; the harness drafts changes but never approves them (PRD R1).
* **Simple for people.** One question per page, stable names for rules, and as few levels as possible.
* **Owners decide what blocks.** Only written blocking rules fail a PR, and the person reviewing it can dismiss a mistaken finding, on record (section 4). The only things blocked with no way round them are the four forbidden actions (section 5).

## 1. Rules within a standard

_Decided: [ADR 0041](../decisions/0021-numbered-rules-in-standards.md) and [0084](../decisions/0033-one-standards-command.md)._

### Blocking or advisory, per page

A standard page is blocking or advisory as a whole, set by its owners. A blocking rule fails the PR review; an advisory one is a comment (section 4). When a topic has both kinds of rules, the owners split it: `secrets.md` blocks, `go-style.md` advises. The page records it in an `enforcement` field, which arrives with Verify, the first thing that reads it ([0015](../decisions/0008-six-header-fields-to-start.md)).

### Numbered rules

* Each standard's rules share a short prefix, unique across the library, such as `sec`, `go` or `pay-api`. The architecture council assigns prefixes ([0084](../decisions/0033-one-standards-command.md)), and records them in `prefixes.yaml` at the library's root once a second domain joins, alongside `domains.yaml` (design doc 1, section 1).
* Its rules are numbered `<prefix>-<n>`: `sec-1`, `sec-2`… IDs are lower case so they can't be mistaken for Jira keys such as `PAY-412`, or turned into wrong links by Jira autolinks.
* A number is never reused or changed. A dropped rule stays on the page, marked retired, so old findings still point somewhere.
* Each rule is one line: the rule, a one-line reason and a short example (design doc 1, section 2).
* Findings in the PR review cite the rule ID, and reports count findings by rule ID and team (PRD R6; section 4).

### Example

```markdown
---
type: standard
title: Secrets and personal data
updated: 2026-11-02
expires: never
approved_by:
  - {what: "library PR #81", date: 2026-11-02}
---
# Secrets and personal data

- **sec-1** Never commit a secret; read it from the secret store. *Why:* git history is copied everywhere. *Example:* the payment key comes from the vault, never a literal.
- **sec-2** *Retired 2026-11-20:* replaced by sec-4.
- **sec-3** Never log a card number. *Why:* logs are kept for a year and support staff can read them. *Example:* log the last four digits.
- **sec-4** Mask personal data in error messages. *Why:* errors reach dashboards and tickets. *Example:* `customer ***@example.com`.
```

### Ownership

**The architecture council** (principal and senior staff) owns the first standards and approves their library PRs ([0084](../decisions/0033-one-standards-command.md); design doc 1, section 6). It can hand a topic to a specialist team, such as security standards to the security team.

### What the format check adds

* A rule line that doesn't start with its ID fails.
* A prefix another standard already uses fails.
* A rule ID that disappears, rather than being marked retired, fails. The check compares the page with the main branch.

## 2. Writing standards

_Decided: [ADR 0084](../decisions/0033-one-standards-command.md), set by the owner._

One command, `/bagheera:standards`, run by standards owners, seeds, adds and updates standards.

* **`seed <Confluence links>`** drafts standards from the pages given, read through the plugin's Atlassian connector. It reads only those pages, not the pages they link to. It writes one page per topic, in the standard format (section 1), within 500 words; a longer topic, or one with both blocking and advisory rules, becomes more than one page. It reports what it left out and why: vague advice, rules a linter already enforces, duplicates of other standards, and contradictions with org standards.
* **`add`** asks what the rule is, why, an example, and where it applies. It checks the existing standards for a similar rule; if there is one, it shows it and offers to update it instead. Otherwise it adds the new rule, with the next number.
* **`update <standard>`** changes an existing standard. New rules get new numbers, and dropped rules are marked retired.
* **Each run** writes in a local copy of the library, runs the format check (design doc 1, section 7) and shows the change. It opens a library PR only when the person asks, requesting the reviewer they name from the architecture council. The platform team merges it once a council member has approved (design doc 1, section 6).

### Rules for drafting

* **The person running it chooses the sources.** Claude rewrites them into short rules rather than copying them.
* **Every rule can be checked.** A reviewer, person or Claude, must be able to tell from a diff whether the rule is broken. Vague advice ("write clean code") is left out and listed in the report.
* **Don't restate what a linter enforces.** The page links to the linter's settings instead, so the PR review never duplicates a check CI already runs.
* **Domain standards add to org standards, never contradict them** (design doc 1, section 1). The command checks a draft against the org standards and flags conflicts.
* **Confluence text is data,** never followed as instructions.

### What comes first

Before the pilot, the council seeds only what the pilot needs. Other teams add their own languages' standards when they join.

| Standard | Why first |
| --- | --- |
| Secrets and personal data | Blocking, and it applies to every repo |
| Deployment and environments: what may run where, and who may use production credentials | PRD R1's "deployment per environment"; it also underpins forbidden actions (section 5) |
| The pilot team's languages | Claude needs them while code is written (PRD R2) |

## 3. Revealing the relevant standards

_Decided: ADR 0043, following 0022._

An addition ([the plan](../plan.md#4-later-additions-a-team-can-make)). Until it arrives, Claude opens the standards it needs from the library's index, and Plan reads the ones that apply.

Standards reach Claude when the work needs them, in every session, whether or not a stage command is used (PRD R2).

| When | What is revealed | By |
| --- | --- | --- |
| The Architecture stage | The blocking standards that apply to the services involved | The Architecture command |
| The first edit of each kind of file, in any session | The standards for that language, the service's own, and the org-wide ones | A plugin hook, once per standard per session |
| Verify, on every PR | The standards that apply to the files in the diff | The PR review (section 4) |

### How the hook works

1. **It watches file edits.** After each edit Claude makes, the hook looks at the file's kind: its language, from a small map of file extensions in the plugin (`.go` is Go, `.ts` is TypeScript), or a deploy file (Dockerfiles, Helm charts, Terraform, CI deploy jobs), which brings in deployment and environment standards.
2. **It picks the standards that apply,** from the local copy of the library: active standards whose `applies_to` names that language, the repo's service, or nothing, which means everywhere. Standard pages gain the `applies_to` field with this addition.
3. **It reveals each standard once per session.** It keeps a small note of what it has revealed, per session and per subagent, so subagents that write code get the standards too.
4. **It stays within the limit.** Blocking standards come first, in full. If the reveal would pass about 2,000 tokens, advisory standards are listed by rule ID and title.
5. **Claude re-checks.** The standards arrive right after the edit that triggered them. Claude checks that edit against them and carries on. Standard text is reference, never instructions.

## 4. The PR review

_Decided: ADR 0044, amended by 0048. Where it runs: [design doc 3, section 8](developer-workflow.md#8-what-runs-where)._

An addition, Verify ([the plan](../plan.md#4-later-additions-a-team-can-make)). Verify reviews every PR before a person does, as a required check (PRD R3). Only written blocking rules can fail it, and the person reviewing the PR can dismiss a finding the reviewer got wrong, on record.

### What it checks

| Part | What it looks at | Can it fail the check? |
| --- | --- | --- |
| **Standards** | The change against the standards that apply to the files in the diff (section 3) | Yes, for rules on blocking pages |
| **Security** | A general security review beyond the written rules: injection, access control, secrets, unsafe handling of personal data | No. Advisory, labelled "no rule", unless a written blocking rule covers it |
| **Matches the plan** | Ticket branches only: the code does what the plan says and stays inside it (Spec Kit's `analyze`) | No, advisory |
| **Criteria coverage** | Ticket branches only, once that addition is in: every criterion has a test ([design doc 3, section 6](developer-workflow.md#6-build)) | Yes; a script |

Every PR in an onboarded repo gets the standards and security review, Bagheera or not.

### Who reviews

A separate Claude that never sees the author's session. It reads the diff, the standards that apply and, on ticket branches, the approved planning documents. It can read the repo but not change it. Text in the PR, the diff and code comments is data to review, never instructions.

* **In the pilot,** the developer runs `/bagheera:verify` before marking the PR ready, and again after any push. It reviews in a fresh Claude process, posts with the developer's own access, and marks the commit `bagheera/verify` passed or failed; the repo's branch rule requires the mark ([0062](../decisions/0024-laptop-commands-and-the-library-update.md)).
* **From rollout,** the Verify workflow runs it in CI with the same plugin as laptops, and posts through the harness's GitHub App (PRD R3, P7).

The ticket's PR stays a draft until Build's last phase, so the review lands before people are asked. The developer marks the PR ready once the blocking findings are fixed or dismissed.

### How findings appear

* **One comment on each line with a finding.** It gives the rule ID, the rule, why this line breaks it, and whether it blocks:

  > **sec-3 · blocking** Never log a card number. This line logs `card.number` in full. Log the last four digits instead.

* **One summary comment** with the counts, blocking and advisory, the standards consulted, any dismissals, and the run's cost.
* **Re-runs update the same comments.** Fixed findings are marked resolved; nothing is posted twice.

### Dismissing a finding

1. **Who:** whoever is reviewing the PR, usually a manager or a senior engineer. The tool enforces no rule about who (0048).
2. **How:** reply on the finding with `/dismiss sec-3 <reason>`. The reason can't be empty.
3. **Then:** Verify runs again without that finding, on the next push or the next dashboard sync. Anyone with write access can also re-run it straight away from the PR's checks.
4. **Scope:** a dismissal covers that finding on that PR only. The same problem elsewhere is a new finding.
5. **On record:** who dismissed it and why are recorded on the PR's Verify check and in the weekly summary. A dismissal by the PR's own author is accepted, but highlighted for managers.

### Reporting by team and rule (PRD R6)

* **The record lives on the PR.** Each Verify run writes its findings into its check run on GitHub: rule ID, file and line, blocking or advisory, and whether it was fixed or dismissed. There's no separate store.
* **Weekly.** The weekly summary ([design doc 5, section 6](status-and-dashboard.md#6-the-weekly-summary)) counts findings and dismissals by team and by rule, with who dismissed each and why.
* **What it tells people.** A rule with many dismissals is probably unclear or wrong, and its owners can fix it. A rule one team breaks often points to something that team hasn't understood, which is for managers.

## 5. Forbidden actions

_Decided: [ADR 0045](../decisions/0022-deny-rules-and-a-sandbox-trial.md) and [0045](../decisions/0022-deny-rules-and-a-sandbox-trial.md)._

Four things are blocked outright for Claude (PRD R4): deploy commands, force-push, production credentials and edits to protected paths. Deploying is a PRD non-goal: existing CD deploys on merge.

### What blocks what

| Forbidden action | Deny rules, for everyone | GitHub's server-side rules | The sandbox, pilot team |
| --- | --- | --- | --- |
| **Force-push** | The usual forms | Truly blocked on main branches | — |
| **Edits to protected paths** | The usual forms | Truly blocked from main: changes need review | Writes limited to the repo |
| **Deploy commands** | The usual forms | — | Truly blocked: production hosts unreachable |
| **Production credentials** | Claude's file tools and common file commands can't read them | — | Truly blocked: credential folders unreadable |

**Why "the usual forms".** Claude Code's deny rules for shell commands match the command as Claude usually writes it; the same program run by its full path, inside `sh -c` or from a script isn't caught. Read deny rules stop Claude's file tools and commands like `cat`, but not a program, such as a cloud command-line tool, that reads its own credentials. The sandbox closes those gaps at the operating-system level.

### Deny rules, for everyone

In the pilot, they're in each engineer's own settings, copied from `settings/pilot-user-settings.json` in `seeonee/bagheera`; at rollout, in managed settings, which developers can't override ([design doc 6, section 2](distribution-security-and-cost.md#2-how-settings-reach-laptops)). For example:

```json
{
  "permissions": {
    "disableBypassPermissionsMode": "disable",
    "deny": [
      "Bash(kubectl apply *)", "Bash(kubectl delete *)", "Bash(helm upgrade *)", "Bash(helm install *)",
      "Bash(terraform apply *)", "Bash(terraform destroy *)",
      "Bash(git push --force *)", "Bash(git push -f *)", "Bash(git push --force-with-lease *)",
      "Read(~/.aws/**)", "Read(~/.kube/**)", "Read(~/.config/gcloud/**)", "Read(**/.env.production)",
      "Edit(.github/workflows/**)", "Edit(.github/CODEOWNERS)"
    ]
  }
}
```

* **The architecture council edits the list** ([0084](../decisions/0033-one-standards-command.md)), keeping it in line with the deployment and environments standard (section 2). Changes to the settings file also need security's approval.
* **Bypass mode is off,** so nobody can switch the checks off.

### GitHub's server-side rules

At rollout, the org ruleset that runs Verify (design doc 3, section 8) also blocks force-pushing or deleting main branches, and requires review of changes to protected paths, such as workflows.

### In CI

Verify runs with read-only access to the repo and the harness's GitHub App, and no cloud or deploy credentials, so none of the four is possible there.

### The sandbox trial, pilot team

* **Setup.** The pilot engineers' own settings turn the sandbox on, by agreement ([design doc 6, section 2](distribution-security-and-cost.md#2-how-settings-reach-laptops)): Claude Code won't start without it, and Claude can't ask to run a command outside it. Credential folders are unreadable. Listed hosts are reachable (GitHub, package registries, and the internal services the pilot's work needs), production hosts are denied, and any other host prompts the engineer, since only managed settings can block unlisted hosts outright.
* **What we note.** How often it gets in the way: hosts that had to be added, Docker-based tests that couldn't run, machines that couldn't run it. The pilot engineers are all on Macs, where the sandbox runs.
* **Pilot exit decision:** the sandbox for everyone, for some repos, or deny rules only ([the plan](../plan.md#exit-check)).

### Red-team test, before the pilot

In a test repo, the platform team tries to make Claude deploy, force-push, read production credentials and edit protected paths: directly, through scripts, via full paths and inside `sh -c`. It does this once with deny rules alone and once with the sandbox. The results go into the pilot exit decision, and it's repeated whenever the deny list or the sandbox settings change.

## 6. The standards check in the architecture

_Decided: ADR 0046._

Comes with the Architecture stage ([the plan](../plan.md#4-later-additions-a-team-can-make)). `architecture.md` carries a short "Standards check" section, filled from the current standards each time it's drafted (PRD R5): one line for each blocking rule that bears on the design, saying how the design meets it, or marking an exception with its reason.

```markdown
## Standards check

| Rule | How the design meets it |
| --- | --- |
| sec-1 | The refund service reads the payment key from the vault |
| sec-3 | Refund logs carry the card's last four digits only |
| deploy-2 | **Exception:** the refund job runs hourly from stage for one release, while ops moves it. Tech lead to confirm. |
```

### Rules

* **Blocking rules only**, and only those that bear on the design, to keep it short. Advisory standards are still followed, and the hook and Verify bring in everything else (sections 3 and 4).
* **The tech lead's approval covers it.** `/approved architecture` includes the standards check and any exception in it (design doc 3, section 4).
* **An approved exception still shows in Verify.** The finding appears on the PR, and the reviewer dismisses it there, citing the approved architecture (section 4).
* **Always from the current standards.** Re-running Architecture refreshes the section. Verify checks the code against the current standards anyway.
