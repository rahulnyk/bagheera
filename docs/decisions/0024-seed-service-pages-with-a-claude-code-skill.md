---
status: accepted
date: 2026-10-28
deciders: RN (owner)
---
# Seed the first service pages with a Claude Code skill

## Context and problem statement

Existing services need a first-draft page from a one-off automated pass over their code (PRD K8). [0009](0009-seed-service-pages-with-deepwiki-open.md) chose DeepWiki-Open, as PRD §8 prefers. Looking at what it takes to run it showed:

* it can't use an Anthropic API key directly: it needs a LiteLLM proxy (plus its database) and a local embedding model (Ollama), so about four extra processes on the laptop that runs it;
* a Claude subscription can't be used; it needs an Anthropic API key or Bedrock;
* its output is its own wiki format, so we would need a conversion step into our page format;
* its README now points to a hosted "2.0" successor, so the open-source version's future is unclear.

Meanwhile, design doc 1 fixed the service page's format, writing rules and checks, which a Claude Code pass can follow directly.

## Considered options

* A. A seeding skill in the plugin, run in Claude Code inside each service repo
* B. DeepWiki-Open through its bundled LiteLLM proxy, with an Anthropic API key and local Ollama embeddings
* C. DeepWiki-Open through AWS Bedrock (only if the organisation uses Bedrock, PRD Q8)
* D. DeepWiki-Open through Anthropic's OpenAI-compatible endpoint (Anthropic says it is not for production use)

## Decision outcome

Chosen option: A. A skill in the plugin applies the knowledge agent's page format and writing rules to a repo's code as it stands, and writes a draft service page in which every statement is inferred and cites a file at a commit. The draft goes through the same checks and owning-team review as any other inferred change ([0013](0013-merge-only-restated-approved-work-without-a-person.md)). If the drafts for the first real repo are poor, we fall back to option B.

### Consequences

* Good, because it adds no tools, processes or credentials: it runs on a Claude subscription or an Anthropic API key, whichever the plan turns out to be (PRD Q7, Q8).
* Good, because it writes our format directly, with a source on every statement, so no conversion step is needed.
* Good, because it reuses the knowledge agent's rules and the library's checks and review.
* Bad, because Claude Code reads what it judges relevant rather than indexing every file, so a large repo's corners can be missed. A fixed list of places to look and the owners' review offset this.
* Bad, because a run counts against the subscription's usage limits, which may pace seeding across many repos.
* Neutral: it departs from PRD §8, §9 and Appendix B, which name DeepWiki-Open. K8 itself is met. The wording change is listed for the owner in [design/README.md](../design/README.md#prd-wording-to-update).

## More information

Detail in [design/seeding.md](../design/seeding.md). A parser-based code map (an AST) was also discussed: it describes the code's structure, not what a service is for, so it doesn't meet K8 on its own. It may later help check that `depends_on` links match the code.

Amended by [0077](0077-short-seeding-report-no-deepwiki-fallback.md): no DeepWiki-Open fallback.
