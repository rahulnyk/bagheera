---
status: proposed
date: 2026-10-24
decision-makers: RN, security
---

# CI runs claude-code-action with OIDC workload identity to the Anthropic API, a GitHub App token, and pinned models per job

## Context and Problem Statement

The architecture says CI runners use short-lived OIDC credentials, never long-lived keys, and to use a smaller model for curation and lint. Which credentials and which models?

## Decision Drivers

* No long-lived API keys in repo secrets where avoidable
* Predictable cost per job
* Same plugin in CI as on laptops

## Considered Options

* Model auth: A. claude-code-action workload identity federation inputs (`anthropic_federation_rule_id`, `anthropic_organization_id`, `anthropic_service_account_id`, `anthropic_workspace_id`); B. `anthropic_api_key` org secret
* GitHub auth: 1. GitHub App via `actions/create-github-app-token`, passed as `github_token`; 2. default `GITHUB_TOKEN`; 3. a PAT

## Decision Outcome

Chosen: "A" + "1". Fall back to B only if federation isn't enabled on the org's Anthropic account, with the key in an org secret limited to the workflows that need it.

| Job | Model | `--max-turns` |
| --- | --- | --- |
| wiki-curate | `claude-sonnet-5-5` (judgement, injection resistance) | 30 |
| wiki-lint (contradiction pass only; other checks are deterministic) | `claude-haiku-5-5` | 15 |
| session digest (laptop) | `claude-haiku-5-5` | 1 |
| PR review, verify, demo (Phase 3) | `claude-sonnet-5-5` | 40 |

Plugins load in CI with the action's `plugin_marketplaces` and `plugins` inputs, pinned to a tag.

### Consequences

* Good, because no API key sits in repo secrets.
* Good, because a separate Anthropic workspace for CI gives a clean cost line (architecture, Cost note).
* Bad, because federation needs one-time setup in the Anthropic console.

### Confirmation

Phase 1: the curator workflow runs with no `ANTHROPIC_API_KEY` secret present.

## More Information

Inputs checked against claude-code-action `docs/usage.md` (Oct 2026). Depends on the GitHub and Anthropic API assumptions; under Bedrock/Vertex this becomes `use_bedrock`/`use_vertex` with cloud OIDC.
