# Design docs

The [architecture](../architecture.md) says what the parts are. These docs say how each area works, in enough detail to build it. We write them one at a time, and each one in small steps:

1. I bring one question, with 2–3 options and a recommendation.
2. You decide.
3. I write that section, record any real decision as an ADR in [decisions/](../decisions/), and commit.

A doc is done when you approve it. Then we start the next. After all six, the phased plan (`docs/plan.md`) follows.

## The docs, in order

| # | Doc | What it settles | PRD requirements | Status |
| --- | --- | --- | --- | --- |
| 1 | [`knowledge-library.md`](knowledge-library.md) | How the knowledge library is structured, where each piece of knowledge comes from, how it is curated, checked and corrected, and how sessions read it | K1–K7, K9, K10 | **In progress** (1 of 9) |
| 2 | `seeding.md` | How DeepWiki-Open drafts the first service pages and how those drafts are reviewed | K8 | Not started |
| 3 | `developer-workflow.md` | The stage commands, how Spec Kit is carried, what each stage writes, how approval works, the build and demo steps | P1–P7, C1–C4, C6 | Not started |
| 4 | `standards-and-review.md` | Standards format and ownership, which standards each session gets, the PR review, blocking forbidden actions | R1–R5 | Not started |
| 5 | `status-and-dashboard.md` | Where status data lives, the status model, the dashboard, notifications, the weekly summary, the Jira link | V1–V7, C5 | Not started |
| 6 | `distribution-security-and-cost.md` | How the plugin reaches every machine, CI credentials, spend limits, adoption and cost reporting, changing the harness safely | D1–D4, P4, V6 | Not started |

**Why this order.** The knowledge library is the heart of the design and the first rollout phase (PRD §9, Foundations), and the other docs refer to it. Seeding follows because it fills the library. The developer workflow comes next because standards, status and the dashboard all depend on what each stage writes. Distribution comes last because it depends on the Claude plan, which isn't known yet.

## Steps planned for each doc

These are the questions I expect to bring, in order. They will change as we learn.

**1. Knowledge library**
1. Provenance and trust: how each statement records its source, and which sources are trusted. **Done:** [0012](../decisions/0012-record-provenance-per-statement.md)
2. Merge rules under caution: what, if anything, may enter the library without a person
3. Structure: folders, page types, and a layout that can split later ([0007](../decisions/0007-one-knowledge-library.md))
4. Page format: required fields, the Open Knowledge Format shape
5. What each stage adds, and when feature entries are written
6. The curation run: trigger, inputs, what the knowledge agent may touch, how changes are grouped
7. Checks: format, links, allowed paths, sources, injected instructions
8. Corrections: failed checks, rejected proposals, contradictions, stale pages, the 90-day human verification, who looks after them
9. How sessions read it: what is loaded at start and how much

**2. Seeding**
1. How DeepWiki-Open reaches a model and an embedding model on your machine
2. From DeepWiki's export to page drafts in our format
3. How drafts are reviewed and marked verified
4. A per-repo runbook

**3. Developer workflow**
1. How Spec Kit is carried ([0004](../decisions/0004-carry-spec-kit-in-the-plugin.md))
2. Command names ([0003](../decisions/0003-stage-names-from-the-prd.md) and the naming register)
3. What each stage writes, and the feature folder layout
4. Approval: the `/approved` comment, the stage check, and recorded overrides ([0002](../decisions/0002-soft-approval-by-comment.md))
5. Open questions and how answers reach the next stage
6. Build: tests first, one PR per phase, criteria IDs as test names
7. Demo on the stage environment ([0011](../decisions/0011-stage-environment-for-demos.md))
8. What runs locally and what runs in CI

**4. Standards and review**
1. Standard page format and ownership
2. Choosing the standards relevant to a session
3. The PR review: what it checks, blocking versus advisory findings
4. Forbidden actions: deny rules, and whether a guard hook is needed
5. Project principles generated from the standards (R5)

**5. Status and dashboard**
1. Where status data lives ([0005](../decisions/0005-view-only-dashboard.md))
2. The status model: stages, states, who it's waiting on, recorded overrides
3. What the dashboard is built on
4. Views and filters
5. Notifications
6. The weekly summary
7. The Jira link ([0006](../decisions/0006-jira-link-only.md))

**6. Distribution, security and cost**
1. Plugin packaging and the marketplace
2. How settings reach laptops (depends on the Claude plan)
3. CI credentials
4. Spend limits and reporting
5. Adoption and cost by team
6. How changes to the harness are reviewed and released

## Open items to resolve inside the docs

| Item | Why it's open | Resolved in |
| --- | --- | --- |
| Soft approvals versus PRD C3 "refuses to start" | [0002](../decisions/0002-soft-approval-by-comment.md) proposes a check with recorded overrides | Doc 3, step 4 |
| Carrying Spec Kit in the plugin versus the PRD's "Spec Kit with a preset and extension" | [0004](../decisions/0004-carry-spec-kit-in-the-plugin.md) | Doc 3, step 1 |
| Jungle Book component names versus PRD §8, Appendix B and CLAUDE.md | [0010](../decisions/0010-naming.md) proposes about five themed names | Naming register, as components come up |
| Caution versus PRD K4 and the "updated within a day" target | [0008](../decisions/0008-caution-and-provenance-for-the-knowledge-library.md) | Doc 1, step 2 |
| Status data in the knowledge library versus PRD K1's page types | [0005](../decisions/0005-view-only-dashboard.md) mentions a wiki folder as one option | Doc 5, step 1 |
| DeepWiki-Open needs an Anthropic API key via LiteLLM, not a Claude subscription | [0009](../decisions/0009-seed-service-pages-with-deepwiki-open.md) | Doc 2, step 1 |
| Claude plan and model access (Q7, Q8) not known yet | Settings delivery, CI sign-in and team cost reporting depend on it; the docs keep both paths open | Doc 6 |

## Naming register

Themed names are proposed to the owner first ([0010](../decisions/0010-naming.md)). Commands, files, workflows and skills keep plain names.

| Component | Plain name | Themed name | Status |
| --- | --- | --- | --- |
| The product | Delivery harness | Bagheera | Approved (PRD) |
| The knowledge library | Knowledge library | — | To propose in doc 1 |
| The knowledge agent | Knowledge agent | — | To propose in doc 1 |
| The dashboard | Dashboard | — | To propose in doc 5 |
