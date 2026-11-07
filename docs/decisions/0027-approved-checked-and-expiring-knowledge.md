---
status: accepted
date: 2026-11-07
deciders: RN (owner)
---
# A person approves every library change, each change is checked once, and knowledge expires

## Context and problem statement

Every session reads the library, so a wrong or planted statement reaches everyone (PRD K5), and pages go stale as code changes (K6). How is the library kept trustworthy without a standing process to re-check it?

## Considered options

* A. A person approves every change, each change is checked once when it's made, and every page expires, after which it's a hint
* B. Changes that restate approved work merge by themselves, and a weekly check re-verifies pages every 90 days
* C. A, plus a source and trust level on every statement and screens for planted instructions

## Decision outcome

Set by the owner: A.

* **A person approves every change,** with an `/approved library` comment ([0002](0002-anyone-approves-with-a-comment.md)). Agents never merge.
* **Outside text is data.** PR text, tickets, documents and diffs are read as data, never followed as instructions.
* **Sources per PR.** A library PR lists the merged PRs and documents its changes came from; pages carry no source on each statement.
* **Knowledge expires.** Every page carries an `expires` date. Past it, the page is a hint, not a fact: sessions check it against the code before relying on it. Service pages and feature entries expire 90 days after their last approved change; decisions and standards don't expire until a later change retires them and sets a date. A library PR can set any page to another date, or to `never`.
* **Renewed by any approved change** that touches the page, or by a PR that changes only its date.
* **Checked once, when it's made.** `/bagheera:update-library` keeps pages to their type's fields and shape and keeps links between pages working, and the format check runs on every library change ([0031](0031-the-format-check-in-each-library.md)).
* **Unchanged pages aren't re-checked;** they stop being trusted when they expire.

### Consequences

* Good, because the library has one rule for merging and one check, run when something changes.
* Good, because nobody spends time re-checking pages that haven't changed.
* Bad, because an unchanged page that's still right loses trust after 90 days until someone renews it.
* Bad, because nothing looks for contradictions or orphan pages; contradictions are flagged when used ([0028](0028-the-newest-page-wins-approvers-recorded.md)).
* Bad, because a person's review is the only guard against planted text reaching a page.

## More information

Detail in [design/knowledge-library.md](../design/knowledge-library.md).
