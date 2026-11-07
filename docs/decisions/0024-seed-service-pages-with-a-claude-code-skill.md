---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# Seed service pages with our own Claude Code skill, on DeepWiki-Open's method

## Context and problem statement

Existing services need a first-draft page from a one-off automated pass over their code (PRD K8). DeepWiki-Open has worked out how to turn a repo into grounded documentation, but running it takes a LiteLLM proxy and its database, a local embedding model (Ollama), and an Anthropic API key or Bedrock rather than a Claude subscription, and it writes its own wiki format. What drafts the pages?

## Considered options

* A. Our own skill in the plugin, borrowing DeepWiki-Open's method
* B. Our own skill, without borrowing
* C. Run DeepWiki-Open, through its LiteLLM proxy with local embeddings, or through Bedrock

## Decision outcome

Chosen by the owner: A.

* **`seed-service-page`** runs in Claude Code inside the service's repo and writes draft pages in the library's format.
* **Plan first, then write.** From the file tree and the README, it lists the repo's services and, for each section of each page, the files to read. The person running it confirms the plan.
* **Write only from those files.** Anything not found in them is left off the page and listed as not worked out.
* **Skip what isn't the service's own code:** vendored code, build output, lock files, and env and secret files, so seeding never reads credentials.
* **A read-only code reader** does the reading: a plugin subagent that can only read and search files. Nothing builds, runs or tests the code.
* **A short report** is the seeding PR's description: the commit read, the files and line ranges behind each section, conflicts between docs and code, and what it couldn't work out.
* **If drafts are poor,** we improve the skill, or the team writes the page.
* **Our own instructions.** If any of DeepWiki-Open's prompt text is copied (commit `f89c59a`, MIT licence), its notice goes in `third_party/deepwiki-open/`.

### Consequences

* Good, because there's nothing new to install or run: it works on a Claude subscription or an Anthropic API key.
* Good, because it writes our page format directly and reuses the library's checks and review.
* Good, because reviewers get the exact lines behind each section, and the file filter keeps secrets out.
* Bad, because Claude reads what the plan lists rather than indexing every file, so a large repo's corners can be missed; the plan step and the team's review offset this.
* Bad, because a run counts against the subscription's usage limits.

## More information

Detail in [design/seeding.md](../design/seeding.md).
