# Standards and review

_Status: approved by the owner, 2026-10-30; changed by the owner, 2026-11-04 ([0084](../decisions/0084-one-standards-command-seeds-adds-and-updates.md)) · Design doc 4 of 6 · [All design docs](README.md)_

_Team walkthrough deck: [standards-and-review-walkthrough.pptx](standards-and-review-walkthrough.pptx) ([PDF](standards-and-review-walkthrough.pdf))._

Standards are written once by their owners and applied everywhere: in Claude's context when it designs and writes code, checked on every PR, with dangerous actions blocked outright (PRD G2). This doc says how rules are named, how the first standards get written, how sessions see the relevant ones, what the PR review checks and how its findings are reported, how forbidden actions are blocked, and how project principles come from the standards.

Covers PRD R1–R6. It builds on [design doc 1](knowledge-library.md), which set the standard page's header, shape, layout and owner approval (sections 3 and 4) and when stages reveal standards (section 9), and on [design doc 3](developer-workflow.md), which set where Verify runs (section 9).

## Changes since approval

_Where this section and the rest of the doc differ, this section wins. The first system is in [`docs/plan.md`](../plan.md)._

| Part | Now |
| --- | --- |
| Section 2: writing the first standards | `/bagheera:standards` replaces `/bagheera:draft-standard`, in the first system. `seed <Confluence links>` drafts standards from the org's Confluence pages; `add` asks questions, checks for a similar standard and offers to update it instead; `update` changes one. Each run opens a library PR only when the person asks. The drafting rules stay ([0084](../decisions/0084-one-standards-command-seeds-adds-and-updates.md)) |
| Sections 3–5 | The standards hook, the PR review and the managed deny rules are additions to the first system ([0069](../decisions/0069-first-system-delivery-path-and-knowledge-library.md)); the pilot's deny rules are in each engineer's own settings ([0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md)) |

## Principles

* **Written once, by their owners.** Standards are owner-approved pages in the library; the harness can propose changes but never approves them (PRD R1; design doc 1, section 2).
* **Simple for people.** One question per page, stable names for rules, and as few levels as possible (design doc 3).
* **Owners decide what blocks.** Only written blocking rules fail a PR, and the person reviewing it can dismiss a mistaken finding, on record (section 4). The only things blocked with no way round them are the four forbidden actions (section 5).

## 1. Rules within a standard

_Decided: [ADR 0041](../decisions/0041-blocking-per-page-numbered-rules.md), building on [0015](../decisions/0015-typed-page-headers.md) and [0017](../decisions/0017-brief-pages-with-fixed-shapes-and-length-limits.md)._

### Blocking or advisory, per page

A standard page is `blocking` or `advisory` as a whole, set by its owners. A blocking rule fails the PR review; an advisory one is a comment (section 4). When a topic has both kinds of rules, the owners split it: `secrets.md` blocks, `go-style.md` advises.

### Numbered rules

* Each standard page has a `prefix` in its header, unique across the library, such as `sec`, `go` or `pay-api`.
* Its rules are numbered `<prefix>-<n>`: `sec-1`, `sec-2`… IDs are lower case so they can't be mistaken for Jira keys such as `PAY-412`, or turned into wrong links by Jira autolinks.
* A number is never reused or changed. A dropped rule stays on the page, marked retired, so old findings still point somewhere.
* Each rule is one line: the rule, a one-line reason and a short example (design doc 1, section 4).
* Findings in the PR review cite the rule ID, and reports count findings by rule ID and team (PRD R6; section 4).

### Example

```markdown
---
type: standard
id: standards/org/secrets
title: Secrets and personal data
domain: org
owners: ["@seeonee/architecture-council"]
status: active
updated: 2026-11-02
expires: never
approved_by:
  - {who: "@meera", what: "library PR #81", date: 2026-11-02}
approval: <link to library PR #81>
applies_to: []
enforcement: blocking
prefix: sec
---
# Secrets and personal data

- **sec-1** Never commit a secret; read it from the secret store. *Why:* git history is copied everywhere. *Example:* the payment key comes from the vault, never a literal.
- **sec-2** *Retired 2026-11-20:* replaced by sec-4.
- **sec-3** Never log a card number. *Why:* logs are kept for a year and support staff can read them. *Example:* log the last four digits.
- **sec-4** Mask personal data in error messages. *Why:* errors reach dashboards and tickets. *Example:* `customer ***@example.com`.
```

`applies_to: []` means the standard applies everywhere (design doc 1, section 4).

### Ownership

Each standard page names its owning team in `owners`. **The architecture council** (principal and senior staff) owns the first standards ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md)), and can later hand a page to a specialist team, such as security standards to the security team, by changing its `owners`. The owners file GitHub uses is generated from `domains.yaml` and each standard page's `owners`, so the right team's approval is required for every change (design doc 1, section 6).

### The prefix register

The council assigns prefixes and writes them down in `prefixes.yaml`, at the root of the library beside `domains.yaml` ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md)). It owns the file, so adding a prefix is a small library PR the council approves. The generated `index.md` lists every prefix with its page and owners, so anyone can look one up.

```yaml
sec:    {standard: standards/org/secrets,    owners: ["@seeonee/architecture-council"]}
deploy: {standard: standards/org/deployment, owners: ["@seeonee/architecture-council"]}
go:     {standard: standards/org/go,         owners: ["@seeonee/architecture-council"]}
```

### What the library checker adds

* A standard without a `prefix`, with one already used elsewhere, or with one that isn't in `prefixes.yaml` for that page, fails the header check.
* A rule line that doesn't start with its ID fails the shape check.
* A rule ID that disappears, rather than being marked retired, fails the shape check. The checker compares the page with the main branch.

## 2. Writing the first standards

_Decided: [ADR 0042](../decisions/0042-draft-first-standards-from-owners-documents.md)._

Claude drafts each standard from the documents its owners point to; the owners edit and approve it.

### How a standard is drafted

1. **Start.** A member of the architecture council, or a platform engineer with them, runs `/bagheera:draft-standard` and gives it the topic, the owning team, where it applies, whether it blocks, a prefix from the register (section 1), and the source documents (pasted in or attached).
2. **Draft.** Claude writes one page in the standard format (section 1; design doc 1, section 4): numbered rules, each with a one-line reason and a short example, within 500 words. A longer topic, or one with both blocking and advisory rules, becomes more than one page.
3. **Report.** It lists what it left out and why, for the PR description: rules a linter already enforces, statements too vague to check, duplicates of other standards, and anything that contradicts an org standard.
4. **PR.** It opens a library PR to the page's owners with the person's own GitHub access. The page is written as `active`, with `approval` set to that PR, because it can only merge once the owners approve it; nothing is edited afterwards. The library checks run, including both planted-instruction screens, since the source documents are outside text (design doc 1, section 7).
5. **Approve.** The owners edit the PR as they see fit and approve it. As an owner-written page, their approval means they vouch for every line (design doc 1, section 1).

### Rules for drafting

* **The owners choose the sources.** Claude rewrites them into short rules rather than copying them; long passages copied word for word are flagged by the pattern screen anyway.
* **Every rule can be checked.** A reviewer, person or Claude, must be able to tell from a diff whether the rule is broken. Vague advice ("write clean code") is left out and listed in the report.
* **Don't restate what a linter enforces.** The page links to the linter's settings instead, so the PR review never duplicates a check CI already runs.
* **Domain standards add to org standards, never contradict them** (design doc 1, section 3). The skill checks a draft against the org standards and flags conflicts in the report.

### What comes first

Foundations writes only what the pilot needs. Other teams add their own languages' standards when they join.

All three are owned by the architecture council to start ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md)).

| Standard | Why first |
| --- | --- |
| Secrets and personal data | Blocking, and it applies to every repo |
| Deployment and environments: what may run where, and who may use production credentials | PRD R1's "deployment per environment"; it also underpins forbidden actions (section 5) |
| The pilot team's languages | Build reveals them while code is written (PRD R2) |

### Changing a standard later

The same skill updates a page: run it on the existing page with the change or new source material, and it opens a PR to the owners. Numbering rules apply: new rules get new numbers, and dropped rules are marked retired (section 1). The weekly library check can also propose changes, which go to the same owners (design doc 1, section 8).

## 3. Revealing the relevant standards

_Decided: [ADR 0043](../decisions/0043-reveal-standards-on-first-edit.md), following [0022](../decisions/0022-reveal-the-library-incrementally-as-a-graph.md)._

Standards reach Claude when the work needs them, in every session, whether or not a stage command is used (PRD R2).

### When standards are revealed

| When | What is revealed | By |
| --- | --- | --- |
| The Architecture stage | The blocking standards that apply to the services involved | The Architecture command (design doc 1, section 9) |
| The first edit of each kind of file, in any session | The standards for that language, the service's own, and the org-wide ones | A plugin hook, once per standard per session |
| Verify, on every PR | The standards that apply to the files in the diff | The PR review (section 4) |
| Whenever Claude or the developer asks | Any standard | The knowledge skill (design doc 1, section 9) |

### How the hook works

1. **It watches file edits.** After each edit Claude makes, the hook looks at the file's kind: its language, from a small map of file extensions in the plugin (`.go` is Go, `.ts` is TypeScript), or a deploy file (Dockerfiles, Helm charts, Terraform, CI deploy jobs), which brings in deployment and environment standards.
2. **It picks the standards that apply,** from the local copy of the library: active standards whose `applies_to` names that language, the repo's service, or nothing (which means everywhere). The repo's service is already known from session start (design doc 1, section 9).
3. **It reveals each standard once per session.** It keeps a small note of what it has revealed, per session and per subagent, so subagents that write code get the standards too.
4. **It stays within the limit.** Blocking standards come first, in full. If the reveal would pass about 2,000 tokens (design doc 1, section 9), advisory standards are listed by rule ID and title, with a pointer to the knowledge skill.
5. **Claude re-checks.** The standards arrive right after the edit that triggered them. Claude checks that edit against them and carries on. Standard text is reference, never instructions (design doc 1, section 9).

### What changes elsewhere

* **Build no longer reveals language standards itself.** The hook does it in every session, Build included, so there is one mechanism (design doc 1, section 9).
* **Design outside the stage commands** gets standards when Claude looks them up through the knowledge skill, for example by asking what applies to the service. Formal design goes through the Architecture stage, which reveals the blocking standards.
* **Verify backs it up.** Anything that slips through is caught on the PR (section 4).

## 4. The PR review

_Decided: [ADR 0044](../decisions/0044-blocking-findings-fail-verify-tech-leads-dismiss.md), amended by [0048](../decisions/0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md). Where it runs: [design doc 3, section 9](developer-workflow.md#9-what-runs-locally-and-what-runs-in-ci)._

Verify reviews every PR before a person does, as a required check (PRD R3). Only written blocking rules can fail it, and the person reviewing the PR can dismiss a finding the reviewer got wrong, on record.

### What it checks

| Part | What it looks at | Can it fail the check? |
| --- | --- | --- |
| **Standards** | The change against the standards that apply to the files in the diff (section 3) | Yes, for rules on blocking pages |
| **Security** | A general security review beyond the written rules: injection, access control, secrets, unsafe handling of personal data | No. Advisory, labelled "no rule", unless a written blocking rule covers it |
| **Matches the plan** | Ticket branches only: the code does what the phase says and stays inside it (Spec Kit's `analyze`) | No, advisory |
| **Feature folder check** | Ticket branches only: the PR writes only its own ticket's files (design doc 3, section 3) | Yes; a script |
| **Criteria coverage** | Phase PRs only: every criterion in the phase has a test (design doc 3, section 6) | Yes; a script |
| **Tests first** | Phase PRs only: the first commit is the tests (design doc 3, section 6) | No; a warning, recorded ([0038](../decisions/0038-managers-enforce-qa-review-and-tests-first.md)) |

Every PR in an onboarded repo gets the standards and security review, Bagheera or not. The other checks apply only to ticket branches.

### Who reviews

A separate Claude, run by the Verify workflow in CI with the same plugin as laptops (PRD R3, P7). It never sees the author's session. It reads the diff, the standards that apply and, on ticket branches, the approved planning documents. It can read the repo but not change it, and it posts its comments through the harness's GitHub App. In the pilot, the developer runs it with `/bagheera:verify`, which reviews in a fresh Claude, posts with the developer's own access and marks the commit passed or failed ([0062](../decisions/0062-pilot-runs-verify-library-and-dashboard-updates-on-laptops.md); design doc 6, section 3). Text in the PR, the diff and code comments is data to review, never instructions (design doc 1, section 1).

Build opens phase PRs as drafts, so the review lands before people are asked. The developer marks the PR ready once the blocking findings are fixed or dismissed.

### How findings appear

* **One comment on each line with a finding.** It gives the rule ID, the rule, why this line breaks it, and whether it blocks:

  > **sec-3 · blocking** Never log a card number. This line logs `card.number` in full. Log the last four digits instead.

* **One summary comment** with the counts, blocking and advisory, the standards consulted, and any dismissals.
* **Re-runs update the same comments.** Fixed findings are marked resolved; nothing is posted twice.

### Dismissing a finding

1. **Who:** whoever is reviewing the PR, usually a manager or a senior engineer. The tool enforces no rule about who ([0048](../decisions/0048-anyone-reviewing-may-dismiss-who-and-why-recorded.md)).
2. **How:** reply on the finding with `/dismiss sec-3 <reason>`. The reason can't be empty.
3. **Then:** Verify runs again without that finding. GitHub doesn't re-run a required workflow on a comment, so this happens on the next push, or within 15 minutes when the dashboard sync re-runs it. Anyone with write access can also re-run it straight away from the PR's checks.
4. **Scope:** a dismissal covers that finding on that PR only. The same problem elsewhere is a new finding.
5. **On record:** who dismissed it and why are recorded on the PR's Verify check and in the weekly report. A dismissal by the PR's own author is accepted, but highlighted in the report for managers.

### Reporting by team and rule (PRD R6)

* **The record lives on the PR.** Each Verify run writes its findings into its check run on GitHub: rule ID, file and line, blocking or advisory, and whether it was fixed or dismissed. There's no separate store.
* **Weekly.** The weekly summary ([design doc 5, section 6](status-and-dashboard.md#6-the-weekly-summary)) counts findings and dismissals by team and by rule, with who dismissed each and why, and @mentions each standard's owners on the rows about their rules.
* **What it tells people.** A rule with many dismissals is probably unclear or wrong, and its owners can fix it. A rule one team breaks often points to something that team hasn't understood, which is for managers.

### Cost

Each review is a Claude run on every PR, so it counts towards CI spend. PRD §7 sets a budget per merged PR. Each run records its cost (design doc 6, section 5), and whether to cap spend is decided after the pilot.

## 5. Forbidden actions

_Decided: [ADR 0045](../decisions/0045-deny-rules-now-sandbox-trial-in-pilot.md)._

Four things are blocked outright for Claude (PRD R4): deploy commands, force-push, production credentials and edits to protected paths. Deploying is a PRD non-goal: existing CD deploys on merge.

### What blocks what

| Forbidden action | Deny rules, for everyone | GitHub's server-side rules | The sandbox, pilot team only |
| --- | --- | --- | --- |
| **Force-push** | The usual forms | Truly blocked on main branches | — |
| **Edits to protected paths** | The usual forms | Truly blocked from main: changes need their owners' review | Writes limited to the repo |
| **Deploy commands** | The usual forms | — | Truly blocked: production hosts unreachable |
| **Production credentials** | Claude's file tools and common file commands can't read them | — | Truly blocked: credential folders unreadable |

**Why "the usual forms".** Claude Code's deny rules for shell commands match the command as Claude usually writes it. Anthropic's documentation says such a rule "isn't a security boundary around the program": the same program run by its full path, inside `sh -c` or from a script isn't caught. Read deny rules stop Claude's file tools and commands like `cat`, but not a program, such as a cloud command-line tool, that reads its own credentials. The sandbox closes those gaps at the operating-system level.

### Deny rules, for everyone

Set in managed settings, which developers can't override; how they reach laptops is design doc 6. For example:

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

* **A default list ships with the scaffolding.** The harness's setup in `seeonee/bagheera` seeds a default deny list, and the architecture council edits it from there ([0047](../decisions/0047-architecture-council-owns-standards-prefixes-deny-list.md)). The council keeps it in line with the deployment and environments standard (section 2), and changes reach laptops through managed settings (design doc 6).
* **Bypass mode is off,** so nobody can switch the checks off.

### GitHub's server-side rules

Set by the org managers in the same org ruleset that runs Verify (design doc 3, section 9):

* main branches can't be force-pushed or deleted;
* changes to protected paths, such as workflows and the owners file, need their owners' review before they can merge.

### In CI

Verify runs with read-only access to the repo and the harness's GitHub App, and no cloud or deploy credentials. So none of the four is possible there in the first place (design doc 3, section 9).

### The sandbox trial, pilot team only

* **Setup.** The pilot engineers' own settings turn the sandbox on, by agreement ([0059](../decisions/0059-pilot-settings-in-engineers-own-settings-file.md); design doc 6, section 2): Claude Code won't start without it, and Claude can't ask to run a command outside it. Credential folders are unreadable. Listed hosts are reachable (GitHub, package registries, and the internal services the pilot's work needs), production hosts are denied, and any other host prompts the engineer, since only managed settings can block unlisted hosts outright.
* **What we measure.** How often it gets in the way: hosts that had to be added, Docker-based tests that couldn't run, machines that couldn't run it. The pilot engineers are all on Macs, where the sandbox runs; native Windows would need WSL2 later.
* **Pilot exit decision** (`docs/plan.md`): the sandbox for everyone, for some repos, or deny rules only. If it's adopted, the PRD's "there is no sandbox" assumption changes.

### Red-team test, before the pilot

In a test repo, the platform team tries to make Claude deploy, force-push, read production credentials and edit protected paths: directly, through scripts, via full paths and inside `sh -c`. It does this once with deny rules alone and once with the sandbox. The results go into the pilot exit decision, and it's repeated whenever the deny list or the sandbox settings change.

### Note on the PRD

Until the pilot decision, deploy commands and production credentials are blocked only in the usual forms, outside the pilot team's sandboxed machines. So R4's "blocked outright" isn't fully met for those two in the meantime. The decision before rollout closes this.

## 6. Project principles from the standards

_Decided: [ADR 0046](../decisions/0046-standards-check-in-the-architecture.md)._

There's no separate principles file. The architecture itself carries a short standards check, filled from the current standards each time it is drafted (PRD R5).

### The standards check

`architecture.md` has a "Standards check" section. When `/bagheera:architecture` runs, it fills the section from the blocking standards revealed for the services involved (section 3): one line for each rule that bears on the design, saying how the design meets it, or marking an exception with its reason.

```markdown
## Standards check

| Rule | How the design meets it |
| --- | --- |
| sec-1 | The refund service reads the payment key from the vault |
| sec-3 | Refund logs carry the card's last four digits only |
| deploy-2 | **Exception:** the refund job runs hourly from stage for one release, while ops moves it. Tech lead to confirm. |
```

### Rules

* **Blocking rules only**, and only those that bear on the design, to keep it short. Advisory standards are still followed, and Build and Verify bring in everything else (sections 3 and 4).
* **The tech lead's approval covers it.** `/approved architecture` includes the standards check and any exception in it (design doc 3, section 4).
* **An approved exception still shows in Verify.** The finding appears on the PR, and the reviewer dismisses it there, citing the approved architecture (section 4). So the exception is on record, and reaches the standard's owners in the weekly report.
* **Always from the current standards.** Re-running Architecture refreshes the section. If a standard changes after approval, nothing is regenerated behind anyone's back; Verify checks the code against the current standards anyway.

### Note on the PRD

R5 says the pipeline's "templates and project principles are generated from the standards". Here the template stays fixed and its standards section is filled from them, with no separate principles file. The wording change is listed for the owner in [the design index](README.md#prd-wording-to-update).
