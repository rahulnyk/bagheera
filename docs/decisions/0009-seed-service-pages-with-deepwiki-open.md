---
status: superseded by [0024](0024-seed-service-pages-with-a-claude-code-skill.md)
date: 2026-10-26
deciders: RN (owner)
---
# Seed the first service pages with DeepWiki-Open

## Context and problem statement

Existing services need a first-draft page from a one-off automated pass over their code (PRD K8). The architecture draft asked whether to use DeepWiki-Open, as the PRD prefers, or the knowledge agent alone.

## Considered options

* DeepWiki-Open, run once per repo
* The knowledge agent drafting pages directly from the code

## Decision outcome

Chosen option: DeepWiki-Open. The owner will run it locally. Its output is a draft and goes through the same provenance and review path as any other untrusted input ([0008](0008-caution-and-provenance-for-the-knowledge-library.md)).

### Consequences

* Good, because it follows the PRD's preferred building block.
* To check in design doc 2: DeepWiki-Open has no direct Anthropic provider. It reaches Claude through its LiteLLM proxy with an Anthropic API key (or through Bedrock), and it needs a separate embedding model, for example Ollama locally. A Claude subscription on its own won't work.
