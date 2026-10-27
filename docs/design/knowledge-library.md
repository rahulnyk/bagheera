# Knowledge library

_Status: in progress (sections 1–2 of 9) · Design doc 1 of 6 · [All design docs](README.md)_

The knowledge library is the org's single, shared set of markdown pages: standards, one page per service, decisions, and one entry per shipped feature (PRD K1). Every Claude Code session reads it (K7), and every merged change can add to it (K3). This doc says how it is structured, where each piece of knowledge comes from, how it is curated, checked and corrected, and how sessions read it.

Covers PRD K1–K7, K9 and K10. Seeding the first service pages (K8) is [design doc 2](README.md).

## 1. Provenance and trust

_Decided: [ADR 0012](../decisions/0012-record-provenance-per-statement.md), following [0008](../decisions/0008-caution-and-provenance-for-the-knowledge-library.md)._

Every statement in the library can be traced to where it came from, and says how far we trust it.

### Trust levels

| Level | What it covers | Examples | Can it be in the library? |
| --- | --- | --- | --- |
| **Owner-approved** | Written or approved by the named owner of the page or folder | A standard approved by the security owners; a decision record approved by its decision owners | Yes |
| **Approved work** | Material approved through the delivery flow | A spec marked `/approved` by the PM; an architecture marked `/approved` by the tech lead | Yes, with a link to the approval |
| **Inferred** | Worked out by the knowledge agent or DeepWiki-Open from code or approved documents, and not yet checked by a person | "payments-api calls ledger-svc", read from a merged diff | Yes, marked as inferred until a person verifies it. When it may enter without a person is section 2. |
| **External** | Text from outside the approved flow | Ticket descriptions, PR descriptions, pasted or linked documents, chat | Never as a statement. At most a link inside a source note, until a person confirms it. |

A person can **verify** an inferred statement. Its source note then records who verified it and when, and it counts as owner-approved. How verification is requested and tracked is section 8.

### How each statement records its source

**Statements the knowledge agent writes** (service pages, feature entries, proposed decisions) each end with a footnote. The footnote gives the trust level, the source with a link, and the date. A statement is a sentence or list item that states a fact; headings and links need no footnote.

```markdown
payments-api retries a failed charge up to 3 times over 24 hours.[^s1]
payments-api calls ledger-svc to record each charge.[^s2]

[^s1]: approved work · PAY-412 spec, `/approved` by @asha on 2026-10-20 · <link to the approval comment>
[^s2]: inferred · from the diff of seeonee/payments-api#431, merged 2026-10-24 · not yet verified
```

**Pages written by their owners** (standards, and decision records written by people) record provenance once, in the page's front matter: owner, approved by, link to the approval, date. The owner vouches for the whole page, so they need no footnotes.

GitHub renders footnotes, so people see them as numbered references. They are plain Markdown, which keeps the format portable (K10).

### What a check enforces

The checks themselves are section 7. For provenance, a check rejects:

* any statement written by the agent without a footnote
* any footnote whose link doesn't point to a source inside our GitHub org
* any statement whose only source is external

### What it means for Claude

When a session loads a page, Claude sees which statements are settled (owner-approved or approved work) and which are inferred. The session-start instructions tell it to treat inferred statements as hints to confirm in the code, not as facts. Section 9 covers what is loaded.

## 2. Merge rules under caution

_Decided: [ADR 0013](../decisions/0013-merge-only-restated-approved-work-without-a-person.md)._

A change from the knowledge agent enters the library on its own only when a person has already stood behind everything it says.

| The change | Enters the library | Who reviews |
| --- | --- | --- |
| Every new or changed statement cites **approved work** or an **owner-approved** page, and all checks pass | By itself | Nobody; it shows in the change log |
| Any statement is **inferred**, for example read from code | After review | The page's owning team; for a service page, the service's team |
| A new or changed **decision record** | After approval | The decision's owners |
| Any change to a **standard** | After approval | The standard's owners |
| Any check fails, including the injected-instructions screen | Not until fixed | The page's owners (the platform team during the pilot) |
| A statement whose only source is **external** | Never | Nobody; the check rejects it |

**In practice.** Feature entries mostly restate the approved spec, architecture, plan, test plan and demo, so they merge by themselves. Service-page changes read from code get a short review by the service's team. Reviewers see every statement with its source footnote, so they check sources, not prose. When they approve, the inferred statements they approved are marked as verified (section 1).

**One PR never mixes the two kinds.** The knowledge agent puts changes that can merge by themselves and changes that need a person into separate PRs (section 6). How a reviewer's approval is recorded on a library PR is also section 6.

**Effect on the PRD's targets and K4.**

* *Pages updated within a day of a change (≥ 90%):* feature entries meet it on their own; service pages depend on how fast the service's team reviews. Pending library reviews appear on the dashboard and in the weekly report.
* *Pages verified by a person in the last 90 days (≥ 80%):* each review of inferred statements counts as verification, which helps.
* *K4* ("agents may merge service and feature page changes after automated checks") holds in a narrower form: the agent merges only what restates approved work.

**When to revisit.** After the pilot. If reviews turn into rubber stamps, relax to "merge with marks" (inferred statements merge marked, and owners verify later). If anything wrong gets through, tighten to "nothing without a person".

## 3. Structure

_To brainstorm._

## 4. Page format

_To brainstorm._

## 5. What each stage adds

_To brainstorm._

## 6. The curation run

_To brainstorm._

## 7. Checks

_To brainstorm._

## 8. Corrections

_To brainstorm._

## 9. How sessions read it

_To brainstorm._
