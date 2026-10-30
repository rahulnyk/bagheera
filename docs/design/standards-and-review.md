# Standards and review

_Status: in progress · Design doc 4 of 6 · [All design docs](README.md)_

Standards are written once by their owners and applied everywhere: in Claude's context when it designs and writes code, checked on every PR, with dangerous actions blocked outright (PRD G2). This doc says how rules are named, how the first standards get written, how sessions see the relevant ones, what the PR review checks and how its findings are reported, how forbidden actions are blocked, and how project principles come from the standards.

Covers PRD R1–R6. It builds on [design doc 1](knowledge-library.md), which set the standard page's header, shape, layout and owner approval (sections 3 and 4) and when stages reveal standards (section 9), and on [design doc 3](developer-workflow.md), which set where Verify runs (section 9).

## Principles

* **Written once, by their owners.** Standards are owner-approved pages in the library; the harness can propose changes but never approves them (PRD R1; design doc 1, section 2).
* **Simple for people.** One question per page, stable names for rules, and as few levels as possible (design doc 3).

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
owners: ["@seeonee/security"]
status: active
last_verified: {date: 2026-11-02, review: "library PR #81"}
approved_by: "@seeonee/security"
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

Each standard page names its owning team in `owners`. That can differ from the domain's owners: the security team owns security standards, a platform team owns deployment standards. The owners file GitHub uses is generated from `domains.yaml` and each standard page's `owners`, so the right team's approval is required for every change (design doc 1, section 6).

### What the library checker adds

* A standard without a `prefix`, or with one already used elsewhere, fails the header check.
* A rule line that doesn't start with its ID fails the shape check.
* A rule ID that disappears, rather than being marked retired, fails the shape check. The checker compares the page with the main branch.

## 2. Writing the first standards

_Decided: [ADR 0042](../decisions/0042-draft-first-standards-from-owners-documents.md)._

Claude drafts each standard from the documents its owners point to; the owners edit and approve it.

### How a standard is drafted

1. **Start.** An owner, or a platform engineer with them, runs `/bagheera:draft-standard` and gives it the topic, the owning team, where it applies, whether it blocks, a prefix, and the source documents (pasted in or attached).
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

| Standard | Typical owners | Why first |
| --- | --- | --- |
| Secrets and personal data | The security team | Blocking, and it applies to every repo |
| Deployment and environments: what may run where, and who may use production credentials | The platform or operations team | PRD R1's "deployment per environment"; it also underpins forbidden actions (section 5) |
| The pilot team's languages | The pilot domain's tech leads | Build reveals them while code is written (PRD R2) |

### Changing a standard later

The same skill updates a page: run it on the existing page with the change or new source material, and it opens a PR to the owners. Numbering rules apply: new rules get new numbers, and dropped rules are marked retired (section 1). The weekly library check can also propose changes, which go to the same owners (design doc 1, section 8).

## Still to come

3. Revealing the relevant standards, including outside the stage commands
4. The PR review: what it checks, how findings are shown and reported
5. Forbidden actions: deny rules, and whether a guard hook is needed
6. Project principles generated from the standards
