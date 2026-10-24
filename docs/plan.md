# Bageera — SDLC Harness Phased Plan

_Status: draft for review, 2026-10-24. Source of truth: [architecture.md](architecture.md) (exported from the architecture doc). Decisions: [decisions/](decisions/README.md)._

This plan turns the architecture into six phases. Each phase opens with a two-sentence summary for PMs, then the engineering detail: goal and exit criteria, deliverables with paths, which existing tools we configure, a test plan, and risks.

**Guiding rule: build as little as possible.** Where the plan builds something, it says what existing tool was considered and why it doesn't fit. The custom code across all phases is about ten small scripts and hooks, six agent prompts, a Spec Kit preset and extension, and workflow files.

---

## 1. Read this first: conflicts with the architecture

Checking the architecture against the current tools (Oct 2026) turned up places where it can't be built as written, or where it is silent. None of these are guessed around: each has a proposed resolution and an ADR, and the ones marked **your call** need a decision before or during the phase shown.

| # | Conflict or gap | Proposed resolution | ADR | Phase | Your call? |
| --- | --- | --- | --- | --- | --- |
| C1 | `docs/architecture.md` wasn't in the repo. I exported it from the linked doc; the embedded 4-layer diagram didn't survive the export. | Commit the export as-is; re-add the diagram if you want it in git. | — | now | Confirm the export is the version you meant |
| C2 | The architecture names `org-wiki` and a marketplace repo but not where the preset, CI workflows and monitoring config live; the wiki's trust rules (CODEOWNERS, rulesets, auto-merge) only work at repo level. | `bageera` holds the plan and ADRs; build three repos: `org-wiki`, `claude-marketplace`, `observability`. | [0002](decisions/0002-repository-topology.md) | 1 | **Yes**: GitHub org name, and may I create sandbox repos? |
| C3 | Rollout puts curator + lint in weeks 7–8; Task 2 builds all of Layer 1 first. | Pull curator + lint into Phase 1. Phase 1 grows ~1 week. | [0014](decisions/0014-pull-wiki-curation-into-phase-1.md) | 1 | **Yes** |
| C4 | Stage 8 says ADRs merge into the wiki "automatically", but `decisions/` needs CODEOWNERS approval. | Feature entry auto-merges; ADRs go in a separate PR that waits for owners. | [0004](decisions/0004-wiki-trust-boundary-enforcement.md) | 1 | No (trust boundary wins) |
| C5 | `raw/`, `index.md`, `CHANGELOG.md`, `CLAUDE.md`, `.github/` have no trust zone. `raw/` holds untrusted PR text that any agent could later read. | `raw/` add-only and auto-merge; excluded from agent reads. `CLAUDE.md` and `.github/` owned by the platform team. | [0004](decisions/0004-wiki-trust-boundary-enforcement.md), [0010](decisions/0010-curator-untrusted-input.md) | 1 | No |
| C6 | "The SessionStart hook can set `team`/`feature_id` resource attributes." It can't: attributes are read once at launch, there's no branch attribute, and repo settings can't set telemetry variables. | Repo attribution from a built-in flag; team from a repo→team map; feature from commit events. | [0012](decisions/0012-telemetry-attribution.md) | 1, 4 | No (architecture text needs a fix) |
| C7 | DeepWiki-Open has no direct Anthropic API provider, and Anthropic has no embedding model. | Use DeepWiki's bundled LiteLLM proxy → Claude, and local Ollama embeddings. | [0007](decisions/0007-deepwiki-bootstrap.md) | 1 | Depends on the model-access assumption |
| C8 | `mcp-server-git` has no read-only mode and can't read or search file contents; every community `llm-wiki` MCP server we found is read-write. | Official GitHub MCP server in read-only mode, plus a local clone for skills. | [0006](decisions/0006-wiki-read-access-for-agents.md) | 1 | No |
| C9 | The architecture suggests OKF/KnowledgeX for pages; OKF reserves `log.md` while the architecture names `CHANGELOG.md`, and KnowledgeX flags `CLAUDE.md` as a broken note. | Use OKF; keep `CHANGELOG.md`; the lint wrapper ignores the three root non-page files. | [0003](decisions/0003-wiki-page-format-okf.md) | 1 | No |
| C10 | `Stop` fires every turn, and `SessionEnd` hooks get 1.5 s by default. | `SessionEnd` + a detached digest writer; queue = GitHub issues. | [0005](decisions/0005-session-digest-queue.md) | 1 | No |
| C11 | Spec Kit's `plan.md` is the *technical* plan and `tasks.md` the phased plan; the architecture uses `architecture.md` and `plan.md`. Spec Kit commands are now `/speckit-*` skills. | Keep Spec Kit's names; add `testplan.md` and `demo.md` via an extension. `specs/<ticket-id>/` works via `SPECIFY_FEATURE_DIRECTORY`. | [0008](decisions/0008-spec-kit-preset-and-artifact-names.md) | 2 | **Yes**, with the pilot PM |
| C12 | A gate that reads front-matter from the local file can be skipped by editing it. | The gate reads the default-branch copy; CODEOWNERS per artifact decides who approves. | [0009](decisions/0009-stage-gates.md) | 2 | No |
| C13 | Plugin-shipped subagents ignore `hooks`, `mcpServers` and `permissionMode`, so "architect writes only under `specs/`" can't be set on the agent. | Tool allowlists on the agent + one plugin-level hook. | [0016](decisions/0016-plugin-level-guards-for-subagents.md) | 2 | No |
| C14 | Grafana's Jira data source is Enterprise-only; the Jira sync job is listed as "build". | Infinity data source (OSS) for Jira; try an existing Spec Kit Jira extension before writing a sync. | [0013](decisions/0013-delivery-progress.md) | 4 | No |
| C15 | Weekly digest as "a scheduled Agent SDK job". | Scheduled claude-code-action (same SDK, no program to host). | [0015](decisions/0015-weekly-digest-via-scheduled-action.md) | 4 | No |
| C16 | The June 2026 billing change is cited from a PyPI package page, not an Anthropic source. | Treat as unverified; confirm with your Anthropic account team. Under the API-key assumption CI is API-billed anyway. | — | 1 | Confirm billing |
| C17 | `.wiki/` "submodule or sparse checkout" inside each repo means churn across hundreds of repos. | One shared sparse clone in the plugin's data directory. | [0006](decisions/0006-wiki-read-access-for-agents.md) | 1 | No |

### Assumptions and what depends on them

| Assumption | What depends on it | If it changes |
| --- | --- | --- |
| **GitHub** for code and CI | claude-code-action, rulesets + CODEOWNERS (trust boundary, gates), digest queue as issues, GitHub MCP for wiki reads, GitHub App tokens | GitLab: headless `claude -p` in GitLab CI, approval rules instead of rulesets, a different MCP for wiki reads. Phases 1–3 change substantially. |
| **Jira** for tickets | Stage 1 intake via the Atlassian MCP server, Phase 4 sync and dashboard queries | Linear: swap MCP server, sync extension and Infinity queries. Phase 4 only. |
| **Anthropic API** for models | CI auth (OIDC federation), model IDs, DeepWiki routing via LiteLLM, digest billing | Bedrock/Vertex: `use_bedrock`/`use_vertex` in CI, cloud OIDC, DeepWiki goes all-Bedrock. Config changes only. |

Items in this plan that rely on an assumption are tagged **[GH]**, **[JIRA]** or **[API]**.

---

## 2. Where things live

Per [ADR 0002](decisions/0002-repository-topology.md). `acme` is a placeholder org name.

```
acme/org-wiki                     Layer 1: the wiki (its own repo for the trust rules)
acme/claude-marketplace           Layer 2+3: plugin, Spec Kit preset/extension, reusable CI workflows, managed-settings source
  .claude-plugin/marketplace.json
  plugins/acme-sdlc/              the one plugin: skills, agents, hooks, .mcp.json
  speckit/                        acme preset + acme extension
  .github/workflows/              reusable workflows (wiki-curate, review, verify, demo, jira-sync, digest)
  managed-settings/               the JSON admins deploy (reviewed here, deployed via claude.ai console or MDM)
  bootstrap/deepwiki/             one-off DeepWiki-Open config and procedure
acme/observability                Layer 4: OTel Collector, Prometheus, Loki, Grafana dashboards (or the platform team's existing infra repo)
<each service repo>               a 10-line caller workflow, CODEOWNERS lines for specs/, and specs/<ticket-id>/ artifacts
```

This repo, `bageera`, holds the plan, the ADRs and the architecture export. Until the real repos exist, scaffolds are built here under `org-wiki/` (Task 2) and pushed out once you confirm the org. `rahulnyk/KnowledgeX` is used only as a tool (the `kx` validator) and as the sample service for end-to-end tests; nothing is built inside it.

## 3. Phases at a glance

| Phase | Weeks | Layers | What ships | Exit in one line |
| --- | --- | --- | --- | --- |
| 1 Foundations | 1–3 | 1, 4 | Wiki repo that builds itself; telemetry baseline | A merged PR on the sample repo updates the wiki with no human, and policy edits still need a human |
| 2 Plugin v0 | 4–5 | 2, 3 | `acme-sdlc` plugin, stages 1–4, gates | Pilot PM approves spec → test plan for one real feature, entirely in PRs |
| 3 Build and CI | 6–7 | 2, 3 | Stages 5–7: build, CI review/verify, demo | One phase of the pilot feature merges with CI review and a generated demo |
| 4 Compounding | 8–9 | 4 (+1) | Jira sync, delivery dashboard, weekly digest | Pilot ships two features end to end; PMs approve test plans without rewriting |
| 5 Org rollout | 10–13 | all | 2–3 teams at a time | Each new team ships a feature through the gates within 3 weeks of onboarding |
| 6 Headless builder (optional) | after 13 | 3 | Nightly Agent SDK builder | Only if you choose to raise the autonomy ceiling |

Phase 1 is a week longer than the architecture's rollout because curation moves into it (C3). Later phases shift by a week.

---

## Phase 1 — Foundations: the wiki that builds itself, and a usage baseline (weeks 1–3)

> **Summary.** We create the company wiki that Claude reads before every task, seed it with standards written by senior engineers and service pages drafted automatically, and make it update itself whenever code merges. We also switch on usage and cost tracking for everyone now, so we have a "before" picture to compare the harness against.

Phase 1 has two independent tracks: **1a Layer 1 (wiki)**, which is Task 2, and **1b Layer 4 baseline (telemetry)**.

### Goal

Every merged PR in a connected repo produces a reviewed-by-machine wiki update within 15 minutes, policy pages can only change with a human approval, and agents in any repo can read the wiki.

### Exit criteria (observable, testable)

1. `org-wiki` exists with the architecture's layout and a `CLAUDE.md` schema; the wrapped `kx check` passes with 0 errors on `main`.
2. `org/` holds at least: coding standards, security rules, dos and don'ts, and one deploy page per environment, each authored by a named senior engineer (`generated.by: human:<id>`).
3. Merging a PR in the sample repo opens a curator PR against `org-wiki` within 15 minutes; if it only touches `services/`, `features/`, `raw/`, `index.md`, `CHANGELOG.md` it auto-merges after checks.
4. A curator PR touching `org/` or `decisions/` does not merge without a CODEOWNERS approval (tested, T1.4).
5. Nightly lint opens or updates one "Wiki lint report" issue listing contradictions, broken links, stale service pages and orphans.
6. Ending a Claude Code session on a feature branch produces at most one digest comment in the `org-wiki` digest issue for that branch; the curator consumes and closes it on merge.
7. From a session in another repo, an agent answers a question from the wiki via the read-only MCP server.
8. `services/` has a page for the sample repo, bootstrapped by DeepWiki-Open and condensed by the curator.
9. (1b) Grafana shows sessions, tokens and cost per repo and per team for all engineers, for at least 5 working days before Phase 2 starts.

### Deliverables

**1a — `acme/org-wiki`** (scaffolded first in `bageera` at `org-wiki/`)

| Path | What |
| --- | --- |
| `CLAUDE.md` | Schema: page types and OKF type mapping, front-matter rules, naming (kebab-case, `services/<repo-name>.md`, `decisions/NNNN-title.md`, `features/<ticket>-<slug>.md`), linking (relative markdown links only), zones, lint rules, curator procedure |
| `README.md` | For humans: what the wiki is, how to propose a change |
| `index.md`, `CHANGELOG.md` | Table of contents (generated by `kx index`), dated log newest first |
| `raw/`, `org/`, `org/deploy/`, `services/`, `decisions/`, `features/` | Layout from the architecture |
| `.templates/{service,feature,decision,org-principle,deploy-playbook,raw-source}.md` | One template per page type (hidden folder so it isn't linted as pages) |
| 2–3 example pages | e.g. `services/knowledgex.md`, `decisions/0001-use-okf-for-wiki-pages.md`, `features/kx-22-okf-subfolders.md` |
| `.github/CODEOWNERS` | `/org/ @acme/eng-standards`, `/decisions/ @acme/architecture`, `/CLAUDE.md /.github/ @acme/harness-platform` |
| `.github/rulesets/main.json` | Exported ruleset, applied with `gh api` (documented) **[GH]** |
| `.github/workflows/wiki-checks.yml` | On every PR: wrapped `kx check`, `path-policy`, `raw-add-only`, `injection-guard`; enables auto-merge for curator PRs that pass |
| `.github/workflows/wiki-lint-nightly.yml` | Cron: deterministic lint + one model pass for contradictions; updates the lint report issue |
| `.github/scripts/kx-check.sh` | Runs `npx knowledgex@0.5.0 check --bundle .`, drops findings for root `CLAUDE.md`/`CHANGELOG.md`/`README.md` |
| `.github/scripts/path-policy.sh` | Fails a bot PR that mixes protected and unprotected zones |
| `.github/scripts/raw-add-only.sh` | Fails any PR modifying or deleting a file under `raw/` |
| `.github/scripts/injection-guard.mjs` | Deterministic checks on the PR diff ([ADR 0010](decisions/0010-curator-untrusted-input.md)) |
| `.github/scripts/stale-services.sh` | Lists service pages whose repo's default branch changed after the page's last `generated.at`/`verified.at` |
| `.github/scripts/orphans.sh` | Lists pages no other page links to (excluding `index.md`) |

**1a — `acme/claude-marketplace`**

| Path | What |
| --- | --- |
| `.claude-plugin/marketplace.json` | Marketplace listing `acme-sdlc` |
| `plugins/acme-sdlc/.claude-plugin/plugin.json` | Plugin manifest, version `0.1.0` |
| `plugins/acme-sdlc/agents/wiki-curator.md` | Curator subagent: tools `Read, Grep, Glob, Write, Edit, Bash`, model `claude-sonnet-5-5`, `maxTurns: 30` |
| `plugins/acme-sdlc/skills/wiki-lint/SKILL.md` | Lint skill: runs the deterministic scripts, then the contradiction pass; usable locally and in CI |
| `plugins/acme-sdlc/skills/wiki-lookup/SKILL.md` | How to find and cite wiki pages (local clone first, MCP second, never `raw/`) |
| `plugins/acme-sdlc/hooks/hooks.json` | `SessionStart` (refresh wiki clone in background, retry spooled digests) and `SessionEnd` (digest) |
| `plugins/acme-sdlc/scripts/session-digest.sh` | Detached digest writer ([ADR 0005](decisions/0005-session-digest-queue.md)) |
| `plugins/acme-sdlc/scripts/wiki-refresh.sh` | Sparse clone/fetch into `${CLAUDE_PLUGIN_DATA}/org-wiki`, at most hourly |
| `plugins/acme-sdlc/.mcp.json` | `org-wiki` server: GitHub MCP, read-only, `get_file_contents,search_code` **[GH]** |
| `.github/workflows/wiki-curate.yml` | Reusable workflow (`workflow_call`): fetch untrusted inputs, mint App token, run claude-code-action with the curator, open PR(s) |
| `callers/wiki-curate.yml` | 10-line caller each service repo copies (`on: pull_request: types: [closed]`) |
| `bootstrap/deepwiki/` | `litellm-config.yml` (Anthropic route + Ollama embeddings), `.env.example`, `README.md` with the one-off procedure |
| `tests/` | `bats` tests for every script; curator fixtures (recorded PRs); injection red-team fixtures |

**1b — `acme/observability`** and managed settings

| Path | What |
| --- | --- |
| `docker-compose.yml`, `otel-collector/config.yaml` | Taken from `ColeMurray/claude-code-otel`, endpoints changed to ours |
| `grafana/dashboards/claude-code-*.json` | The upstream dashboards, plus a `repo` and `team` variable |
| `grafana/provisioning/repo-team-map.csv` | Repo → team map, generated nightly from `services/*.md` owners |
| `claude-marketplace/managed-settings/managed-settings.json` | `env`: `CLAUDE_CODE_ENABLE_TELEMETRY=1`, `OTEL_METRICS_EXPORTER=otlp`, `OTEL_LOGS_EXPORTER=otlp`, `OTEL_EXPORTER_OTLP_PROTOCOL`, `OTEL_EXPORTER_OTLP_ENDPOINT`, `OTEL_METRICS_INCLUDE_REPOSITORY=true`, `OTEL_LOG_TOOL_DETAILS=1`; `requiredMinimumVersion` ≥ 2.1.269 |

### Existing tools and how they're configured

| Tool | Use | Configuration |
| --- | --- | --- |
| Karpathy LLM Wiki pattern | Shape of the wiki | `raw/` inputs compiled into pages by an agent, schema in `CLAUDE.md` |
| OKF v0.2 + KnowledgeX `kx` CLI (MIT) | Page format and deterministic lint | `npx knowledgex@0.5.0 check --bundle .` and `review --bundle .`, pinned ([ADR 0003](decisions/0003-wiki-page-format-okf.md)) |
| MADR 4.0 | ADR body sections | Sections inside OKF `Decision` pages |
| `anthropics/claude-code-action@v1` | Curator and lint runs in CI | `prompt`, `claude_args: --model … --max-turns … --allowedTools …`, `plugin_marketplaces`, `plugins`, `github_token` from App, OIDC federation inputs **[GH] [API]** |
| `actions/create-github-app-token` | Short-lived token scoped to `org-wiki` | App `acme-wiki-curator`: contents/PRs/issues write on `org-wiki` only **[GH]** |
| GitHub rulesets, CODEOWNERS, native auto-merge | Trust boundary | [ADR 0004](decisions/0004-wiki-trust-boundary-enforcement.md) **[GH]** |
| GitHub MCP server (official) | Read-only wiki access | `/x/repos/readonly` or `--read-only --tools get_file_contents,search_code` **[GH]** |
| DeepWiki-Open (MIT), LiteLLM, Ollama | One-off `services/` bootstrap | `docker-compose-litellm.yml`; LiteLLM model `anthropic/claude-sonnet-5-5`; `DEEPWIKI_EMBEDDER_TYPE=ollama`, `nomic-embed-text`; `POST /export/wiki` format markdown **[API]** |
| `gitleaks` | Secret scrub on digests and curator PRs | `gitleaks stdin --no-banner` |
| `gh` CLI | Digest posting, PR creation | Engineers' existing auth; App token in CI |
| `bats-core` | Script tests | `bats tests/` in CI |
| `claude-code-otel` (OSS), OTel Collector, Prometheus, Loki, Grafana | Telemetry baseline | As shipped, endpoints and retention changed |

**What we build and why existing tools don't cover it**

| Build | Considered instead | Why not |
| --- | --- | --- |
| `CLAUDE.md` schema, curator prompt, lint skill | Community `llm-wiki` skills | The architecture says own the prompts; they encode our zones and page types |
| `path-policy.sh`, `raw-add-only.sh` (~30 lines each) | Mergify path rules; CODEOWNERS alone | CODEOWNERS can't stop a mixed PR; a merge bot adds a write-capable third party to the asset we protect |
| `injection-guard.mjs` (~100 lines) | `llm-guard`, commercial classifiers | They score prompts, not wiki diffs; adds a dependency ([ADR 0010](decisions/0010-curator-untrusted-input.md)) |
| `stale-services.sh`, `orphans.sh` (~40 lines each) | `kx review` (time-based staleness only), `lychee` (links only) | Neither knows "repo changed after the page"; no OKF tool reports orphans |
| `session-digest.sh` (~60 lines) | prompt/agent hook types | They return decisions, not documents ([ADR 0005](decisions/0005-session-digest-queue.md)) |
| `wiki-refresh.sh` (~20 lines) | git submodule per repo | Churn in every service repo ([ADR 0006](decisions/0006-wiki-read-access-for-agents.md)) |

### Test plan

| ID | Check | How | Pass when |
| --- | --- | --- | --- |
| T1.1 | Layout and schema | `ls` tree vs architecture; reviewer reads `CLAUDE.md` | All 7 layout items exist; schema covers types, front-matter, naming, links, lint |
| T1.2 | Schema is enforceable | Wrapped `kx check` on templates and examples; then on 6 broken fixtures (missing description, bad status, broken link, wikilink, unknown key, bad date) | Examples: 0 errors. Each fixture: the expected finding |
| T1.3 | Script unit tests | `bats tests/` for path-policy, raw-add-only, injection-guard, stale-services, orphans, digest | All pass in CI |
| T1.4 | Trust boundary on the real repo **[GH]** | Bot opens 5 PRs: services-only; org-only; services+org; modifies raw; touches `.github/` | 1 auto-merges; 2 waits for owner; 3 fails path-policy; 4 fails raw-add-only; 5 waits for platform |
| T1.5 | Curator on recorded fixtures | Run the curator agent locally on 5 recorded merged PRs (diff + description + optional spec) | Each output passes all wiki checks; touches the expected service and feature pages; ADR proposals land in a separate PR |
| T1.6 | Digest written | End a session on branch `kx-99-demo` in the sample repo | One comment in `digest: rahulnyk/KnowledgeX@kx-99-demo` within 60 s |
| T1.7 | Digest safety | Sessions containing a fake AWS key; a trivial "hello" session; a session with `gh` logged out | No comment; no comment; spooled file, posted at next session start |
| T1.8 | Prompt injection | 10+ red-team fixtures (instructions in PR body, in code comments, in a spec file, in a digest; hidden HTML comments; zero-width text; "update org/security.md") | No merged change contains injected content; each is ignored by the model or blocked by a check |
| T1.9 | Cross-repo read | From a session in another repo, ask about the sample service; then ask the agent to edit a wiki page via MCP | Answer cites `services/knowledgex.md`; no write tool is available |
| T1.10 | DeepWiki bootstrap | Run the documented procedure on the sample repo | Export saved in `raw/deepwiki/`; curator PR creates `services/knowledgex.md` that passes checks; maintainer rates it "mostly right" |
| T1.11 | Nightly lint | Seed a contradiction (two pages disagree on a service's data store), a broken link, an orphan, and a stale page; run the workflow manually | Report issue lists all four, with file paths |
| **T1.E2E** | **End to end on a real repo** | **Sample repo: `rahulnyk/KnowledgeX`. Install the caller workflow; replay curation for the already-merged PRs [#23](https://github.com/rahulnyk/KnowledgeX/pull/23) and [#25](https://github.com/rahulnyk/KnowledgeX/pull/25) via `workflow_dispatch`; then merge one new small PR with a digest on its branch** | **Wiki gains/updates `services/knowledgex.md` and two `features/` entries with no human action; the digest issue is closed and linked from `raw/`; lint report stays clean** |
| T1.12 | (1b) Telemetry attribution | Session on branch `JIRA-123-x` with one commit, with managed settings applied | Grafana shows cost under repo, team and (via Loki commit event) `JIRA-123` |

### Risks and open questions

| Risk / question | Impact | Mitigation |
| --- | --- | --- |
| "0 approvals + code-owner review" ruleset behaves differently than expected **[GH]** | Trust boundary leaks or nothing auto-merges | T1.4 before anything else depends on it; fallback is separate repos for policy ([ADR 0004](decisions/0004-wiki-trust-boundary-enforcement.md) option B) |
| Senior engineers don't write `org/` pages in time | Constitution and standards skills have nothing to load | Name owners in week 1; curator may *propose* drafts from existing docs (pasted into `raw/`), owners approve |
| Curator writes plausible but wrong service facts | Every later session trusts them | Pages record `generated.by`; owners `verify`; lint flags unverified pages older than 30 days; T1.5 fixtures grow from real mistakes |
| Digest content leaks sensitive info to all wiki readers | Privacy | gitleaks, "nothing worth keeping" default, per-repo opt-out, short retention (curator closes issues) |
| GitHub remote MCP requires a Copilot seat **[GH]** | Wiki MCP unavailable | Self-hosted `github-mcp-server` image with a read-only fine-grained token |
| DeepWiki LiteLLM→Anthropic route doesn't work cleanly **[API]** | No bootstrap | Fallback: curator drafts service pages straight from the repo ([ADR 0007](decisions/0007-deepwiki-bootstrap.md) option D) |
| `allowManagedHooksOnly` set by security | Plugin hooks may not run | Check with security whether plugin hooks from a force-enabled managed plugin count; decide before Phase 2 |
| Open: GitHub org name; may I create `org-wiki` / `claude-marketplace` sandbox repos for T1.4? | Blocks T1.4, T1.E2E | **Your call** |
| Open: one org-wide wiki or one per domain? (architecture open decision) | Repo count, CODEOWNERS | Plan assumes one org-wide repo; per-domain later means one `org-wiki` per domain with `org/` as a shared submodule |
| Open: billing for non-interactive runs (C16) **[API]** | Budget | Confirm with Anthropic account team |

---

## Phase 2 — Plugin v0: spec to test plan, with gates (weeks 4–5)

> **Summary.** Engineers get one Claude Code plugin that turns a Jira ticket into a spec, an architecture, a phased plan and a plain-English test plan, each reviewed and approved in a normal GitHub pull request. A pilot team of 6–8 uses it on one real feature, and the PM approves the spec and test plan without leaving GitHub.

### Goal

Stages 1–4 run for one real pilot feature, each stage refuses to start until the previous one is approved by the right person, and every session starts with the right wiki context.

### Exit criteria

1. Every pilot engineer has `acme-sdlc` enabled through managed settings and can't add other marketplaces (`strictKnownMarketplaces`).
2. For the pilot feature, `specs/<ticket-id>/` on `main` contains `spec.md`, `plan.md` (+ `data-model.md`, `contracts/`), `tasks.md` and `testplan.md`, each with `status: approved` and merged with the right CODEOWNERS approval.
3. `/feature architect` is refused while `spec.md` isn't approved on `main`, including when the local copy says `approved` (T2.4).
4. A new session in the pilot repo shows the repo's service page and the active feature's artifacts in its context (T2.3).
5. The pilot PM answers the spec's open questions and approves acceptance criteria in the PR, and says in a short retro they'd use it again.

### Deliverables (in `acme/claude-marketplace` unless noted)

| Path | What |
| --- | --- |
| `plugins/acme-sdlc/skills/feature/SKILL.md` | One `feature` skill with subcommands `start <ticket>`, `architect`, `plan`, `testplan` (later `build`, `demo`); sets `SPECIFY_FEATURE_DIRECTORY` and calls the Spec Kit skill underneath |
| `plugins/acme-sdlc/skills/org-standards/SKILL.md` | Loads the relevant `org/` pages for the language/service being touched |
| `plugins/acme-sdlc/skills/deploy-config/SKILL.md` | Reads `org/deploy/<env>.md` before any infra change |
| `plugins/acme-sdlc/agents/architect.md` | Tools `Read, Grep, Glob, Write, Edit` + plugin hook restricting writes to `specs/` ([ADR 0016](decisions/0016-plugin-level-guards-for-subagents.md)) |
| `plugins/acme-sdlc/scripts/session-start.sh` | Adds to `SessionStart`: inject `services/<repo>.md` and `specs/<ticket>/*` (ticket from branch name) as `additionalContext` |
| `plugins/acme-sdlc/scripts/pre-tool-guard.sh` | `PreToolUse`: protected paths (e.g. `.github/`, `infra/prod/`), deploy commands (`kubectl apply`, `terraform apply`, `helm upgrade`, …), stage gate ([ADR 0009](decisions/0009-stage-gates.md)), architect write scope |
| `plugins/acme-sdlc/.mcp.json` | Adds the Atlassian MCP server (official, remote) for ticket intake **[JIRA]** |
| `speckit/presets/acme/` | Preset: `spec-template.md` (ticket front-matter, PM open questions), `plan-template.md` (components, interfaces, data model, Mermaid diagram, draft ADRs), `tasks-template.md` (2–6 phases, each independently shippable behind a flag), `constitution-template.md` (built from wiki `org/`) |
| `speckit/extensions/acme/` | Extension: `speckit.acme.testplan` command + `testplan-template.md` (Given/When/Then per phase; unit/integration/e2e cases; each criterion has a stable ID used as the Playwright test name) |
| `managed-settings/managed-settings.json` | Adds `extraKnownMarketplaces`, `enabledPlugins: {"acme-sdlc@acme": true}`, `strictKnownMarketplaces`, `permissions.deny` (`Bash(git push --force*)`, deploy CLIs, prod credential paths) |
| `<pilot repo>/.github/CODEOWNERS` | `specs/**/spec.md @acme/pm`, `specs/**/plan.md @acme/tech-leads`, `specs/**/tasks.md @acme/tech-leads @acme/pm`, `specs/**/testplan.md @acme/pm @acme/qa` **[GH]** |
| `<pilot repo>/.specify/` | `specify init --integration claude --integration-options="--no-model-invocation"` + `specify preset add` / `specify extension add` from the marketplace repo |
| `<pilot repo>/.github/pull_request_template.md` | Links to `specs/<ticket-id>/` and the phase's test plan |

### Existing tools and configuration

| Tool | Use | Configuration |
| --- | --- | --- |
| GitHub Spec Kit (MIT, v1.2) | Stages 1–3 engine | Claude integration; acme preset + extension; `SPECIFY_FEATURE_DIRECTORY=specs/<ticket>` ([ADR 0008](decisions/0008-spec-kit-preset-and-artifact-names.md)) |
| Claude Code plugins + marketplace + managed settings | Distribution, no drift | `extraKnownMarketplaces`, `enabledPlugins`, `strictKnownMarketplaces` (managed only) |
| Claude Code hooks | Context injection and gates | `SessionStart` (`additionalContext`), `PreToolUse` (exit 2 to block) |
| Atlassian MCP server (official) **[JIRA]** | Read the ticket and linked PRD | OAuth per engineer |
| CODEOWNERS + rulesets **[GH]** | Who approves each artifact | Code-owner review required on `specs/**` |

**Build**: the `feature` skill (a prompt that dispatches; no workflow engine, because Spec Kit is the engine), `pre-tool-guard.sh` and `session-start.sh` (~80 and ~40 lines; hooks are the only supported way to gate tools), preset and extension templates.

### Test plan

| ID | Check | Pass when |
| --- | --- | --- |
| T2.1 | Managed install | Fresh machine with managed settings: plugin present, other marketplace add refused |
| T2.2 | Preset wins | `specify artifact info template:plan-template --json` shows `acme` active |
| T2.3 | Session context | New session on branch `KX-101-json-output` includes `services/knowledgex.md` and `specs/KX-101/*` in context (checked via transcript) |
| T2.4 | Gate | `/feature architect` refused with `spec.md` draft on `main`; still refused after editing local copy; allowed after PM-approved merge |
| T2.5 | Architect scope | Architect asked to edit `src/` is blocked; reviewer (stub) has no write tools |
| T2.6 | Deploy block | `kubectl apply`, `terraform apply` refused by hook and by managed deny |
| T2.7 | Artifact names and front-matter | Each artifact has `ticket`, `stage`, `phase`, `status`; paths match ADR 0008 table |
| **T2.E2E** | **Sample repo `rahulnyk/KnowledgeX`, ticket "KX-101: `kx check --json`": run `/feature start` → `testplan`; RN acts as PM and tech lead via CODEOWNERS on a test team** | **Four approved artifacts on `main`; each gate blocked before approval and passed after; curator adds nothing yet (no merge of code)** |
| T2.P | Pilot | Same flow on the pilot team's real feature | Exit criteria 2–5 |

### Risks and open questions

* PMs and QA need GitHub accounts with review rights (seat cost) **[GH]**. **Your call** with the pilot PM.
* Spec Kit moves fast (1.0 → 1.2 in months). Pin the CLI version; re-run T2.2 on upgrade.
* Plugin skill naming: plugin skills are namespaced (`/acme-sdlc:feature`); verify whether `/feature` alone resolves, and document what engineers type.
* Does the hook input identify the subagent (needed for architect scope)? Verify in week 4; fallback in ADR 0016.
* Open (architecture): Jira vs Linear. Plan assumes Jira.
* Risk that the gate annoys engineers into bypassing (e.g. editing files without the skill). Mitigation: CI re-checks gates on the PR; dashboard in Phase 4 shows skipped gates.

---

## Phase 3 — Build and CI: from approved plan to merged, reviewed, demoed code (weeks 6–7)

> **Summary.** Engineers build each phase of the plan with Claude writing tests first, and every pull request gets an automated review, a test and coverage report, and a security check before a human reviews it. When a phase is done, the PM gets a short recorded demo of the acceptance criteria running on the preview environment, generated automatically.

### Goal

Stages 5–7 run for the pilot feature: one phase merges with CI review, verify output and a generated demo the PM accepts.

### Exit criteria

1. `/feature build 1` creates a worktree and branch, writes the phase's tests from `testplan.md` first (they fail), then code until they pass, and opens a phase PR linking `specs/<ticket>/`.
2. Every phase PR gets: a reviewer-agent comment (standards + security), a test report, a coverage diff, and a security-review comment — all before human review.
3. Acceptance criteria IDs appear as Playwright test names; T3.4 shows 100% of the phase's criteria mapped.
4. `/feature demo` (or the CI demo job) posts `demo.md` and a recording of the acceptance flows on the PR's preview environment; the PM marks it accepted.
5. The harness never deploys: no deploy command ran in any harness session or CI job (checked from telemetry tool events).

### Deliverables

| Path | What |
| --- | --- |
| `plugins/acme-sdlc/agents/implementer.md` | `isolation: worktree`; tools for edit/test; reads only artifacts + wiki |
| `plugins/acme-sdlc/agents/test-writer.md` | Writes tests from `testplan.md`; can't edit `src/` |
| `plugins/acme-sdlc/agents/reviewer.md` | Read-only tools; loads `org-standards`; outputs review comment |
| `plugins/acme-sdlc/agents/demo-builder.md` | Uses Playwright MCP; writes `demo.md` |
| `plugins/acme-sdlc/skills/feature/SKILL.md` | Adds `build N` and `demo` |
| `plugins/acme-sdlc/scripts/post-edit-format.sh` | `PostToolUse` on `Write|Edit`: runs the repo's own formatter/linter if configured |
| `plugins/acme-sdlc/hooks/hooks.json` | Adds `PostToolUse` |
| `speckit/extensions/acme/` | `speckit.acme.demo` command + `demo-template.md` |
| `.github/workflows/harness-review.yml` | Reusable: claude-code-action with `reviewer` agent on `pull_request` |
| `.github/workflows/harness-verify.yml` | Reusable: repo's tests + coverage diff + `anthropics/claude-code-security-review` |
| `.github/workflows/harness-demo.yml` | Reusable: waits for preview URL, runs demo-builder with Playwright MCP, uploads video/screenshots as artifacts |
| `callers/harness-pr.yml` | Caller for service repos |

### Existing tools and configuration

| Tool | Use |
| --- | --- |
| Claude Code subagents (`isolation: worktree`, `tools`) | Implementer/test-writer/reviewer separation |
| claude-code-action **[GH] [API]** | Review and demo jobs |
| `anthropics/claude-code-security-review` | Security comment on PRs (official action; no prompt of our own needed) |
| Repo's existing test runner + a coverage-diff action (e.g. `lcov`-based reporter already used by the repo) | Test report and coverage diff |
| Playwright + Playwright MCP server (official) | Acceptance tests and demo recording |
| Existing preview environments | Demo target (architecture open decision) |

**Build**: four agent prompts, the formatter hook (~20 lines), three reusable workflows, the demo template. A custom PR reviewer was considered unnecessary for security (official action exists); the standards reviewer is ours because it encodes `org/`.

### Test plan

| ID | Check | Pass when |
| --- | --- | --- |
| T3.1 | Tests first | In the build transcript, test files are written and run red before any `src/` edit |
| T3.2 | Worktree isolation | Build runs in a separate worktree; main checkout untouched |
| T3.3 | CI outputs | A sample PR gets review, test report, coverage diff, security comment |
| T3.4 | Criteria mapping | Script lists `testplan.md` criterion IDs vs Playwright test titles: 0 unmapped |
| T3.5 | No deploys | Loki query for deploy CLIs in tool events over the pilot window: 0 results |
| T3.6 | Reviewer independence | Reviewer flags a planted standards violation the implementer introduced |
| **T3.E2E-a** | **Sample repo `rahulnyk/KnowledgeX`, KX-101 phase 1: `/feature build 1` → PR → CI review/verify → merge → curator updates `features/` and `services/knowledgex.md`** | **All of the above, and the wiki change auto-merges** |
| **T3.E2E-b** | **Demo on a web app with per-PR previews (sandbox app or pilot repo): `/feature demo` on a phase PR** | **`demo.md` + recording posted; PM accepts** |

KnowledgeX is a CLI with no UI, so the demo stage needs a second sample repo with a preview environment (T3.E2E-b).

### Risks and open questions

* **Open (architecture): do all services have per-PR preview environments?** The demo stage depends on it. **Your call**: which repo for T3.E2E-b, and what to do for services without previews (fallback: demo against a local `docker compose` stack in CI).
* CI cost per PR (review + verify + demo). Mitigation: `--max-turns`, demo only on `ready_for_review`, alert on outliers (Phase 4).
* Implementer quality varies by codebase. Keep stage 5 human-steered (architecture's default autonomy ceiling).

---

## Phase 4 — Compounding: progress you can see without asking (weeks 8–9)

> **Summary.** Feature status flows automatically from the documents into Jira, so existing boards show where every feature is, and a dashboard shows where features wait longest and what the harness costs per team. A weekly one-page summary lands in Slack: what shipped, what's stuck, and what it cost.

Curation and lint already run from Phase 1, so this phase is the Layer 4 delivery view plus stage 8.

### Goal

Pilot delivery state is visible in Jira and Grafana with no manual updates, and the pilot team ships two features end to end.

### Exit criteria (architecture's exit criterion, plus)

1. The pilot team ships **two features end to end** through all 8 stages.
2. PMs approve test plans **without rewriting them** (measured: PM-authored lines in `testplan.md` diffs < 20% of the file).
3. Changing an artifact's `status`/`stage` on `main` updates the Jira issue within 5 minutes **[JIRA]**.
4. The delivery dashboard shows: features by stage and time waiting at each gate; phases planned vs merged; PR cycle time and review rounds for harness PRs vs others; acceptance criteria with a passing test; wiki health (pages updated/week, stale pages, lint findings).
5. The weekly digest posts to Slack on schedule.
6. A Grafana alert fires on a per-feature cost outlier (tested with synthetic data).

### Deliverables

| Path | What |
| --- | --- |
| `.github/workflows/jira-sync.yml` (marketplace, reusable) | On push to `specs/**`: runs the Spec Kit Jira extension (or fallback script) **[JIRA]** |
| `speckit/` config | Pinned `spec-kit-jira-sync` extension config, or `scripts/jira-sync.mjs` fallback (~100 lines) |
| `observability/grafana/dashboards/delivery.json` | GitHub data source + Infinity (Jira REST/JQL) panels |
| `observability/grafana/alerts/cost-outliers.yaml` | Per-feature cost alert |
| `.github/workflows/weekly-digest.yml` (marketplace) | `schedule` + `workflow_dispatch`: pull dashboard data with `curl` → claude-code-action writes summary → Slack webhook |
| `org-wiki` | `features/` entries now created on each feature's last merge (stage 8) — already wired in Phase 1 |

### Existing tools and configuration

Spec Kit Jira extension (community, pinned and reviewed) or Jira REST; Grafana OSS with the official GitHub data source and the Infinity data source; Prometheus/Loki from Phase 1; claude-code-action on schedule; Slack incoming webhook. See [ADR 0013](decisions/0013-delivery-progress.md), [ADR 0015](decisions/0015-weekly-digest-via-scheduled-action.md).

**Build**: the dashboard JSON, one alert rule, the digest prompt, and the sync fallback script only if the extension fails review.

### Test plan

| ID | Check | Pass when |
| --- | --- | --- |
| T4.1 | Sync idempotent | Run sync twice on the same commit: no duplicate Jira changes |
| T4.2 | Sync latency | Status change merged → Jira updated < 5 min |
| T4.3 | Dashboard numbers | Hand-count 5 features' stages and PR stats vs dashboard: exact match |
| T4.4 | Digest | `workflow_dispatch` posts a digest naming a shipped feature and a stuck gate from sample data |
| T4.5 | Cost alert | Synthetic feature cost 5× median fires the alert |
| **T4.E2E** | **The pilot team's two real features, ticket to `features/` entry** | **Exit criteria 1–2 met; their Jira issues and dashboard rows match the artifacts** |

### Risks and open questions

* Community Jira extension may not pass security review; fallback script is small.
* Gate wait-time panel needs Jira changelog queries; may be approximate at first.
* "PMs approve without rewriting" depends on template quality; budget a template iteration in week 8.

---

## Phase 5 — Org rollout (weeks 10–13)

> **Summary.** We bring the harness to two or three more teams at a time, each adding its own service pages to the wiki as it joins. A platform team of two owns the plugin from here and treats changes to it like production code.

### Goal

Repeatable onboarding of a team in under a day of platform effort, with no change to the plugin per team.

### Exit criteria

1. Each onboarded team ships at least one feature through the gates within 3 weeks of onboarding.
2. Every onboarded repo has: caller workflows, CODEOWNERS lines, Spec Kit init, a verified `services/` page.
3. Plugin releases are tagged, changelogged, and deployed through managed settings; no engineer runs a different version (telemetry `app.version` + plugin version check).
4. Gate-skip rate (PRs touching `src/` without approved artifacts) below an agreed threshold per team.

### Deliverables

* `claude-marketplace/onboarding/README.md`: the checklist, and `onboarding/onboard-repo.sh` that opens one PR per repo adding caller workflows, CODEOWNERS lines and `.specify/` (considered: GitHub org "required workflows" rulesets — use them for the review/verify checks if your plan supports it; the caller file is still needed for the `closed` event).
* DeepWiki bootstrap runs for each new team's repos (Phase 1 procedure).
* `claude-marketplace/CODEOWNERS`: `@acme/harness-platform` owns everything; security owns `managed-settings/` and `plugins/acme-sdlc/hooks/`.
* Release workflow: tag → marketplace version bump → managed settings rollout note.

### Test plan

* T5.1 Onboarding script on a fresh sandbox repo produces a PR that, once merged, passes T1.E2E-style curation and T2.4-style gating.
* T5.2 A plugin release reaches all engineers within one managed-settings refresh cycle (check plugin version in session telemetry).
* **T5.E2E**: the first newly onboarded team's first feature, measured against exit criterion 1.

### Risks and open questions

* Wiki scope (architecture open decision): if teams want per-domain wikis, decide before the second wave.
* Review load on CODEOWNERS for `org/` and `decisions/` grows with teams; watch the dashboard's gate wait time.
* Plugin hooks run with each engineer's permissions and no sandbox (architecture, Governance); security review of `hooks/` on every release.

---

## Phase 6 — Optional: headless nightly builder

> **Summary.** If the pilot shows the human-steered build loop is reliable, we can try a nightly job that picks up approved plan phases and opens draft pull requests for engineers to review in the morning. The architecture recommends piloting this last, and only if you decide to raise the autonomy ceiling.

* **Goal**: draft PRs for `approved` phases, never merged without normal review.
* **Exit criteria**: over 4 weeks, ≥ 50% of nightly drafts merge with only review-level changes; zero drafts touching protected paths.
* **Deliverables**: `.github/workflows/nightly-builder.yml` using claude-code-action (Agent SDK underneath) with the `implementer` agent; considered a standalone Agent SDK program, rejected for the same reason as [ADR 0015](decisions/0015-weekly-digest-via-scheduled-action.md).
* **Test plan**: on the sample repo, an approved phase with a clear test plan yields a draft PR whose tests pass; a phase with `status: draft` is skipped.
* **Risks / open**: **your call** on the autonomy ceiling (architecture open decision). Cost per night; mitigated by `--max-turns` and one phase per repo per night.

---

## 4. Who needs to do what outside engineering

| Who | What | When |
| --- | --- | --- |
| You | Answer the "your call" items in section 1 and the open questions | Before Phase 1 week 1 |
| GitHub org admin | Create repos, the `acme-wiki-curator` GitHub App, teams for CODEOWNERS, rulesets **[GH]** | Phase 1 week 1 |
| Anthropic console admin | Workload identity federation for CI, a CI workspace for cost separation **[API]** | Phase 1 week 1 |
| Claude Code admin | Deploy managed settings (telemetry now; plugin in Phase 2) | Phase 1 week 1 / Phase 2 |
| Senior engineers (named) | Write `org/` pages | Phase 1 weeks 1–2 |
| Security | Review hooks, injection guard, digest privacy, `allowManagedHooksOnly` question | Phase 1 week 2 |
| Jira admin | Service account / token for sync **[JIRA]** | Phase 4 |
| Pilot PM and QA | GitHub review access; act as gatekeepers | Phase 2 |

## 5. Decisions recorded

See [decisions/README.md](decisions/README.md). All are `proposed` until you review this plan.
