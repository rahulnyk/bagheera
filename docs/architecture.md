# SDLC Harness on Claude Code — Architecture

_Exported from the shared architecture doc (rev 11). Only change since export: the plugin's name now follows the [glossary](glossary.md). The embedded diagram will be rebuilt later._

Oct 24, 2026 · @RN

## Summary

Build the harness as **one private Claude Code plugin plus a git-backed wiki repo**, and let existing tools do the rest. Almost nothing here needs a new service: Claude Code already gives you skills, subagents, hooks, MCP servers and managed settings; GitHub's Spec Kit gives you the spec → plan → tasks → implement pipeline; `claude-code-action` runs the same agent in CI; Claude Code's OpenTelemetry output plus Grafana gives you monitoring.

What you actually build is about four things: the plugin (skills, agents, hooks that encode your org's way of working), the wiki schema and its curator agent, a small set of CI workflows, and one Grafana dashboard that joins agent telemetry with Jira/GitHub state. Rough estimate: 2 engineers, 6–8 weeks to a pilot with one team.

The core idea: **every stage of the feature lifecycle writes a markdown artifact into the repo, gated by a human approval, and every merged PR feeds the wiki.** Artifacts in git are the source of truth; the wiki is compiled from them; dashboards read from both.

## Architecture overview

&#91;embedded content: harness architecture · 4 layers\]

Developers drive stages 1–5 locally; CI runs review, demo and wiki curation with the same plugin; every session reads the wiki on start, and the harness never deploys — your existing CD does, on merge.

## Layer 1 — Org knowledge and the LLM wiki

Use Karpathy's LLM Wiki pattern, not a vector database: plain markdown pages an agent compiles and keeps current, stored in one git repo (`org-wiki`). It is human-readable, diffable, reviewable in PRs, and Claude Code reads it natively. Add search later only if the wiki outgrows grep plus an index file.

**Repo layout**

- `raw/` — immutable inputs: merged spec/plan files, ADRs, PR descriptions, incident postmortems, pasted Confluence pages
- `org/` — coding standards, deployment configs per environment, security rules, dos and don'ts (human-owned, agent-proposed edits only)
- `services/<name>.md` — one page per service: purpose, owners, interfaces, data stores, deploy target, gotchas
- `decisions/` — ADR log, one file per decision (MADR template): context, options, choice, reason, date, links
- `features/` — one succinct entry per shipped feature: what, why, services touched, PRs, demo link
- `index.md` + `CHANGELOG.md` — table of contents and a dated log, newest first
- `CLAUDE.md` — the schema: page types, naming, linking and lint rules the curator follows

**How it builds itself**

1. A Claude Code `Stop`/`SessionEnd` hook in the plugin drops a short session digest (decisions made, gotchas found) into a queue — a branch or an issue, not the main wiki.
2. On every merged PR, a `claude-code-action` workflow runs the **wiki-curator** agent: it reads the PR, its spec/plan, and the digests, then opens a PR against `org-wiki` updating the service page, feature log and any ADRs.
3. A nightly **wiki-lint** job flags contradictions, broken links, stale pages (service code changed, page didn't) and orphan pages.
4. Wiki PRs are auto-merged for `features/` and `services/` after checks pass; `org/` and `decisions/` need a CODEOWNERS approval. This propose-then-approve split keeps agents from rewriting policy.

**Codebase knowledge.** For a fast start, run [DeepWiki-Open](https://github.com/AsyncFuncAI/deepwiki-open) (self-hosted, bring your own model) once per repo to seed `services/` pages, then let the curator take over. Don't keep DeepWiki as a second source of truth.

**How agents use it.** Expose the wiki to every session two ways: a git submodule or sparse checkout at `.wiki/` that skills read directly, and a small read-only MCP server (an existing Karpathy-style `llm-wiki` MCP server, or `mcp-server-git`) for cross-repo lookups. Your own [KnowledgeX](https://github.com/rahulnyk/KnowledgeX) / Open Knowledge Format work is a natural fit for the page format if you want portability beyond Claude Code.

## Layer 2 — The feature pipeline

Adopt [GitHub Spec Kit](https://github.com/github/spec-kit) as the backbone and customise its templates rather than writing your own workflow engine. Spec Kit already does constitution → specify → plan → tasks → implement with Claude Code; your work is to (a) point its constitution at `org-wiki/org/`, (b) rewrite the plan template to produce your architecture plan, phased implementation plan and per-phase test plan, and (c) wrap each step in a slash command from your plugin that adds the gates below.

Everything lands in the feature's repo under `specs/<ticket-id>/`, so a PM reviews it as a rendered PR, not in a chat log.

| Stage | Command | Artifact written | Gate (who approves) |
| --- | --- | --- | --- |
| 1. Intake | `/feature start JIRA-123` | `spec.md` from the Jira/Linear ticket + PRD, with open questions | PM answers questions in the PR |
| 2. Architecture | `/feature architect` | `architecture.md`: components, interfaces, data model, Mermaid diagram, ADRs drafted for `org-wiki` | Tech lead approves PR |
| 3. Plan | `/feature plan` | `plan.md`: 2–6 phases, each independently shippable behind a flag | Tech lead + PM |
| 4. Test plan | `/feature testplan` | `testplan.md`: per phase, acceptance criteria in plain English (Given/When/Then), plus unit/integration/e2e cases | PM signs off acceptance criteria; QA signs off cases |
| 5. Build phase N | `/feature build N` | Branch, tests first, code, phase PR | Normal code review + CI |
| 6. Verify | runs in CI | Test report, coverage diff, security review comment on PR | CI green + reviewer |
| 7. Demo | `/feature demo` | `demo.md` script + Playwright recording of the acceptance flows on the preview env | PM accepts |
| 8. Close | auto on last merge | Feature entry and ADRs merged into `org-wiki` | Automatic |

**Design rules**

- Each stage reads only the previous artifacts plus the wiki, so a stage can be re-run without re-running the session.
- Status lives in the artifact front-matter (`status: draft | approved`), and the next command refuses to run until the prior gate is `approved`. A `PreToolUse` hook enforces this.
- Test plans are written before code for each phase, and the build command must make those tests pass. Acceptance criteria double as Playwright test names, which is what makes the demo nearly free.
- The demo is generated, not hand-built: a **demo-builder** agent uses the Playwright MCP server to walk the acceptance flows on the PR's preview environment, record video/screenshots, and write a short narrated script.

## Layer 3 — Execution: the plugin, agents, hooks and CI

Ship everything as one private plugin, `bageera`, in an internal marketplace repo. Roll it out through Claude Code managed settings (`extraKnownMarketplaces` + `enabledPlugins`) so all 100 engineers get the same version and can't drift.

**Plugin contents**

- **Skills** — `feature-*` commands from Layer 2; `org-standards` (loads the relevant `org/` pages for the language/service being touched); `deploy-config` (reads the env's deploy page before any infra change); `wiki-lookup`.
- **Subagents** — `architect` (read-only tools, writes only under `specs/`), `implementer` (runs in a git worktree per phase), `test-writer`, `reviewer` (security + standards, read-only), `demo-builder`, `wiki-curator`. Keeping the reviewer separate from the implementer means the work isn't grading itself.
- **Hooks** — `SessionStart`: inject the service's wiki page and the active feature's artifacts. `PreToolUse`: block edits to protected paths, block deploy commands, enforce the stage gate. `PostToolUse`: run formatter/linter on edited files. `Stop`: write the session digest for the wiki.
- **MCP servers** — GitHub, Jira (or Linear), Playwright, the wiki MCP, and read-only access to your observability tool. Use vendor/official servers; don't write your own.

**Where it runs**

- **On the developer's machine** for stages 1–5: interactive, with the developer steering. This is where most of the value and all of the judgement is.
- **In CI** via [`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action) for stages 6–8: PR review, test-gap analysis, demo recording, wiki curation. Same plugin, loaded in the runner.
- **Optionally headless** via the Claude Agent SDK for one job: a nightly orchestrator that picks up `approved` phases and opens draft PRs. Pilot this last; most orgs find the human-in-the-loop build loop is the reliable part.

**PRs and deploy.** The harness raises PRs but never deploys. Deployment stays in your existing CD (Argo, Spinnaker, GitHub Actions) triggered by merge, so every production change still has a human approval. The PR template links back to `specs/<ticket-id>/` and the phase's test plan.

Note on cost: since June 2026, non-interactive runs (`claude -p`, the Agent SDK and `claude-code-action`) bill against API/Agent SDK credit rather than seat subscriptions, so budget the CI jobs separately.

## Layer 4 — Monitoring and progress

You need two views, and both come mostly free: **agent telemetry** (how the harness is used and what it costs) and **delivery progress** (where each feature is in the pipeline).

**Agent telemetry.** Claude Code emits OpenTelemetry metrics and events natively: sessions, tokens, cost, active time, tool decisions, lines changed, commits and PRs. Turn it on in managed settings (`CLAUDE_CODE_ENABLE_TELEMETRY=1` + OTLP endpoint), send it through an OTel Collector to Prometheus + Loki, and start from the open-source [claude-code-otel](https://github.com/ColeMurray/claude-code-otel) Grafana dashboards. Add `team` and `feature_id` as resource attributes (the `SessionStart` hook can set them from the branch name) so cost and usage roll up per team and per feature.

**Delivery progress.** Don't build a tracker. Each artifact's front-matter (`stage`, `status`, `phase`, `ticket`) is the state, and a small CI job on every push to `specs/**` syncs it to the Jira ticket (status + links). Your existing Jira boards then show the pipeline. For a cross-team view, a Grafana panel queries GitHub and Jira for:

- Features by stage, and time spent waiting at each gate (where PM or tech-lead review is the bottleneck)
- Phases planned vs merged per feature
- PR cycle time and review rounds for harness-raised PRs vs others
- Test-plan coverage: acceptance criteria with a passing automated test
- Wiki health: pages updated per week, stale pages, lint findings

**Weekly digest.** A scheduled Agent SDK job reads the dashboards' data and the week's `features/` log and posts a one-page summary to Slack for you: what shipped, what's stuck at which gate, cost by team.

## Build vs reuse

Roughly 80% is configuration of existing open-source or first-party pieces; the custom work is prompts, templates and glue.

| Need | Reuse | You build |
| --- | --- | --- |
| Agent runtime | Claude Code (interactive), [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) (headless) | Nothing |
| Distribution and governance | Plugin marketplace + managed settings | One marketplace repo, one plugin |
| Spec → plan → tasks → implement | [GitHub Spec Kit](https://github.com/github/spec-kit) (MIT) | Custom templates for architecture, phased plan, PM-readable test plan |
| Org rules and gates | Claude Code skills, subagents, hooks | \~6 agent prompts, \~5 hook scripts |
| CI agent | [claude-code-action](https://github.com/anthropics/claude-code-action) | 4 workflows: review, verify, demo, wiki-curate |
| LLM wiki | Karpathy LLM Wiki pattern; an existing `llm-wiki` skill or MCP server; ADRs in MADR format | Wiki schema (`CLAUDE.md`), curator + lint prompts |
| Codebase bootstrap | [DeepWiki-Open](https://github.com/AsyncFuncAI/deepwiki-open), one-off | Nothing |
| Integrations | Official GitHub, Jira/Atlassian, Playwright MCP servers | Nothing |
| Demo | Playwright + preview environments you already have | Demo-builder prompt and script template |
| Monitoring | Claude Code OTel → Collector → Prometheus/Loki → [Grafana dashboards](https://github.com/ColeMurray/claude-code-otel) | Front-matter → Jira sync job, one delivery dashboard |

**What not to adopt.** Skip general multi-agent frameworks (LangGraph, CrewAI, AutoGen) for this: they would replace Claude Code's tools, permissions and context handling that you'd then have to rebuild. Skip large community "engineering team in a box" plugins as a base; borrow ideas, but own your prompts.

## Governance, security and cost

- **Plugins run as the developer.** Hooks, skills and MCP servers execute with the engineer's permissions and there is no sandbox. Restrict marketplaces to your internal one via managed settings, and review plugin changes like production code (CODEOWNERS on the marketplace repo).
- **Permissions.** Managed settings deny `git push --force`, deploy CLIs, production credentials and writes outside the repo. CI runners use short-lived OIDC credentials, never long-lived keys.
- **Wiki trust boundary.** Agents propose; humans approve anything in `org/` and `decisions/`. Treat PR text, tickets and pasted docs as untrusted input to the curator, since they can carry prompt injection into a page every agent later reads.
- **Data.** If code can't leave your cloud, run Claude via Bedrock or Vertex AI; the harness is unchanged. Self-host DeepWiki and the wiki MCP.
- **Cost.** Seat plans cover interactive use. Budget CI separately: cap `--max-turns`, use a smaller model for curation and lint, run lint nightly not per push, and alert in Grafana on per-feature cost outliers.

## Rollout and open decisions

Start with one team and one real feature, and expand only once the gates are being used rather than skipped.

1. **Weeks 1–2 — Foundations.** Create `org-wiki` with schema and the `org/` pages (standards, deploy configs, dos and don'ts) written by senior engineers. Bootstrap `services/` with DeepWiki-Open. Turn on OTel and the Grafana dashboards for everyone — a usage baseline before the harness.
2. **Weeks 3–4 — Plugin v0.** Spec Kit with custom templates, `feature-*` commands for stages 1–4, `SessionStart`/`PreToolUse` hooks. Pilot with one team of 6–8 on one feature; a PM reviews the artifacts.
3. **Weeks 5–6 — Build and CI.** Stages 5–7: implementer/reviewer agents, CI review and verify workflows, demo-builder on preview envs.
4. **Weeks 7–8 — Compounding.** Wiki-curator on merge, nightly lint, Jira sync, delivery dashboard, weekly digest. Exit criterion: the pilot team ships two features end to end and PMs approve test plans without rewriting them.
5. **Weeks 9–12 — Org rollout.** Two or three teams at a time, each adding its own service pages; a platform team of 2 owns the plugin.

**Open decisions**

- [ ] Tracker: Jira or Linear (picks the MCP server and sync job)
- [ ] Git host: GitHub (claude-code-action) or GitLab (headless CLI in GitLab CI)
- [ ] Model access: Anthropic API vs Bedrock/Vertex for data residency
- [ ] Preview environments: do all services have per-PR envs? The demo stage depends on it
- [ ] Wiki scope: one org-wide repo or one per domain with a shared `org/` core
- [ ] Autonomy ceiling: keep stage 5 human-steered, or pilot the headless nightly builder

## Sources

- [GitHub Spec Kit](https://github.com/github/spec-kit)
- [Claude Code plugin marketplaces: package and publish (2026)](https://dev.to/akaranjkar08/claude-code-plugin-marketplace-2026-package-and-publish-50nd)
- [Enterprise plugin marketplace as a governance layer](https://www.mpt.solutions/your-claude-plugin-marketplace-needs-more-than-a-git-repo/)
- [Claude Code in CI: headless mode, Agent SDK, GitHub Actions](https://claudefolio.com/guides/claude-code-in-ci-headless-sdk-github-actions)
- [Agent SDK credit change for non-interactive runs (June 2026)](https://pypi.org/project/claude-credit-audit/0.1.0/)
- [Karpathy's LLM Wiki pattern explained](https://affine.pro/blog/what-is-llm-wiki)
- [karpathy-llm-wiki skill](https://www.everydev.ai/tools/karpathy-llm-wiki)
- [DeepWiki-Open](https://www.hostinger.com/applications/deepwiki-open)
- [claude-code-otel observability stack](https://github.com/ColeMurray/claude-code-otel)
- [Claude Code monitoring with OpenTelemetry (SigNoz)](https://signoz.io/docs/claude-code-monitoring)
