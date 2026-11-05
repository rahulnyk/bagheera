---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Our seeding skill borrows DeepWiki-Open's method; DeepWiki-Open is never run

## Context and problem statement

Seeding is a Claude Code skill of our own, `seed-service-page` ([0024](0024-seed-service-pages-with-a-claude-code-skill.md)), with no DeepWiki-Open fallback ([0077](0077-short-seeding-report-no-deepwiki-fallback.md)). DeepWiki-Open has worked out how to turn a repo into grounded documentation. Should the skill learn from it?

## Considered options

* A. Our own skill, borrowing DeepWiki-Open's method
* B. Our own skill as designed in doc 2, without borrowing
* C. Run DeepWiki-Open

## Decision outcome

Set by the owner: A. We don't use DeepWiki-Open; we build a Claude skill that borrows from it.

**Borrowed** (from DeepWiki-Open's wiki prompts, commit `f89c59a`, 3 Nov 2026, MIT licence):

* **Plan first, then write.** The skill reads the file tree and the README, and plans before writing: the repo's services and, for each section of each page, the files to read. The person running it confirms the plan.
* **Write only from those files.** Anything not found in them is left out and listed as not worked out.
* **Skip what isn't the service's own code:** vendored code, build output, lock files, and env and secret files, so seeding never reads credentials.
* **Files and line ranges behind each section,** linked at the commit read, in the seeding PR's description, so reviewers check each section in one click. Pages still carry no source on each statement ([0070](0070-knowledge-expires-and-every-change-is-checked-once.md)).

**Left behind:** its server, web app, embeddings and retrieval, Docker, LiteLLM and Ollama; its wiki format and page structure (we write our own page format); diagrams on every page.

**Its text.** We write our own instructions. If any of DeepWiki-Open's prompt text is copied, its MIT notice is kept in `third_party/deepwiki-open/` with the commit, as for Spec Kit ([0028](0028-adapt-spec-kit-once-and-review-it-quarterly.md)). It's adapted once, with no regular review.

### Consequences

* Good, because the skill starts from a method that's been tried on many repos, with nothing new to install or run.
* Good, because reviewers get the exact lines behind each section, and the file filter keeps secrets out of seeding.
* Bad, because the plan step adds one confirmation for the person running it.

## More information

Amends [0024](0024-seed-service-pages-with-a-claude-code-skill.md) and [0077](0077-short-seeding-report-no-deepwiki-fallback.md). Detail in [design/seeding.md](../design/seeding.md) and [plan.md, section 1](../plan.md#the-knowledge-library).
