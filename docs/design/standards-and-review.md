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
approved_by: "@ravi"
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

## Still to come

2. Writing the first standards
3. Revealing the relevant standards, including outside the stage commands
4. The PR review: what it checks, how findings are shown and reported
5. Forbidden actions: deny rules, and whether a guard hook is needed
6. Project principles generated from the standards
