# Decisions

Harness architecture decisions, one file per decision, in [MADR 4.0](https://adr.github.io/madr/) format. New ones start from [adr-template.md](adr-template.md). All are `proposed` until the plan is reviewed.

| ADR | Decision | Status | Plan phase |
| --- | --- | --- | --- |
| [0001](0001-record-decisions-as-madr.md) | Record harness decisions as MADR files in docs/decisions | proposed | — |
| [0002](0002-repository-topology.md) | Three repos: council-rock (wiki), pack (plugin marketplace), chil (monitoring) | proposed | 1 |
| [0003](0003-wiki-page-format-okf.md) | Wiki pages use OKF v0.2 with the KnowledgeX profile, validated by `kx check` | proposed | 1 |
| [0004](0004-wiki-trust-boundary-enforcement.md) | Enforce the wiki trust boundary with a ruleset, CODEOWNERS, a path-policy check and split curator PRs | proposed | 1 |
| [0005](0005-session-digest-queue.md) | Session digests: a SessionEnd hook writes a short digest and posts it to a GitHub issue in org-wiki | proposed | 1 |
| [0006](0006-wiki-read-access-for-agents.md) | Agents read the wiki from a local clone, plus the official GitHub MCP server in read-only mode for cross-repo lookups | proposed | 1 |
| [0007](0007-deepwiki-bootstrap.md) | Bootstrap services/ with self-hosted DeepWiki-Open via its LiteLLM proxy to Claude and local Ollama embeddings | proposed | 1 |
| [0008](0008-spec-kit-preset-and-artifact-names.md) | Adopt Spec Kit through an org preset, and use Spec Kit's artifact names where they overlap | proposed | 2 |
| [0009](0009-stage-gates.md) | A stage gate is an approved artifact on the default branch; a PreToolUse hook checks the default-branch copy | proposed | 2 |
| [0010](0010-curator-untrusted-input.md) | Treat curator input as untrusted: least privilege, quarantined input, deterministic output checks | proposed | 1 |
| [0011](0011-ci-auth-and-models.md) | CI runs claude-code-action with OIDC workload identity to the Anthropic API, a GitHub App token, and pinned models per job | proposed | 1 |
| [0012](0012-telemetry-attribution.md) | Attribute telemetry to repo and team at launch; attribute to features through commit events, not a SessionStart hook | proposed | 1, 4 |
| [0013](0013-delivery-progress.md) | Delivery progress: sync front-matter to Jira with an existing Spec Kit Jira extension; Grafana reads Jira and GitHub through existing data sources | proposed | 4 |
| [0014](0014-pull-wiki-curation-into-phase-1.md) | Build all of Layer 1, including the curator and lint, in Phase 1 | proposed | 1 |
| [0015](0015-weekly-digest-via-scheduled-action.md) | Weekly digest: a scheduled claude-code-action job, not a custom Agent SDK program | proposed | 4 |
| [0016](0016-plugin-level-guards-for-subagents.md) | Enforce subagent limits with tool allowlists plus plugin-level hooks, because plugin subagents ignore hooks, mcpServers and permissionMode | proposed | 2 |
