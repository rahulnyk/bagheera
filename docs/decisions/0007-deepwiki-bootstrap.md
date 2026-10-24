---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Bootstrap services/ with self-hosted DeepWiki-Open via its LiteLLM proxy to Claude and local Ollama embeddings

## Context and Problem Statement

The architecture says to run DeepWiki-Open once per repo to seed `services/` pages, then let the curator take over. We assume Anthropic API for model access. The current DeepWiki-Open (MIT) has no direct Anthropic API provider (it supports Google, OpenAI, OpenRouter, Ollama, Bedrock, Azure, DashScope and a LiteLLM proxy), and it needs an embedding model, which Anthropic doesn't offer (supported: OpenAI, Google, Ollama, Bedrock).

## Decision Drivers

* Keep model access on the Anthropic API as assumed; add no new model vendor
* Code must not leave our control beyond the model provider
* One-off use: cheapest thing that produces a decent first draft

## Considered Options

* A. DeepWiki-Open `docker-compose-litellm.yml`: LiteLLM routes generation to `anthropic/claude-sonnet-5-5`; embeddings from local Ollama `nomic-embed-text`
* B. DeepWiki-Open with Bedrock for both generation (Claude on Bedrock) and embeddings (Titan)
* C. DeepWiki-Open with OpenAI or Google (its defaults)
* D. Skip DeepWiki; have the curator write first-draft service pages directly from the repo with Claude Code

## Decision Outcome

Chosen option: "A", because it keeps generation on the Anthropic API and keeps embeddings local, using only components DeepWiki-Open already ships.

Output path: DeepWiki's `POST /export/wiki` (Markdown) → saved under `org-wiki/raw/deepwiki/<repo>-<date>.md` as a `Source` → the curator turns it into `services/<name>.md` in the schema, as a normal auto-mergeable PR. DeepWiki is shut down afterwards; it is never a second source of truth.

### Consequences

* Good, because no new vendor contract.
* Bad, because the host needs Docker, ~6 GB RAM (compose `mem_limit`) and Ollama.
* Bad, because LiteLLM routing to Anthropic is not one of DeepWiki's shipped presets; its `litellm-config.yml` needs one model entry. A config change, not code.
* Bad, because DeepWiki's export is long-form; the curator must condense it, which is where most of the quality comes from.

### Confirmation

Phase 1 test T1.10: bootstrap run on the sample repo produces a `services/knowledgex.md` that passes lint and that the KnowledgeX maintainer rates "mostly right" on purpose, interfaces and gotchas.

## Pros and Cons of the Options

### B. Bedrock

* Good, because one provider for both.
* Bad, because it contradicts the Anthropic API assumption; becomes the natural choice if the "model access" open decision goes to Bedrock.

### C. OpenAI / Google

* Bad, because it sends source code to a second model vendor.

### D. Skip DeepWiki

* Good, because no extra service at all.
* Bad, because the architecture asks for DeepWiki, and for large repos its retrieval index gives a better first draft than one Claude Code pass. Keep D as the fallback if A fails on the sample repo.

## More Information

Checked against the DeepWiki-Open source on 2026-10-24: `api/config/generator.json`, `api/config/embedder*.json`, `api/routers/wiki.py`. Its README now mostly points to a hosted product; we use only the self-hosted repo.
