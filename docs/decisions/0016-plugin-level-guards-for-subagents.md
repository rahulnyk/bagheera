---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Enforce subagent limits with tool allowlists plus plugin-level hooks, because plugin subagents ignore hooks, mcpServers and permissionMode

## Context and Problem Statement

The architecture specifies `architect` (read-only tools, writes only under `specs/`), `reviewer` (read-only) and `implementer` (git worktree per phase). Claude Code ignores the `hooks`, `mcpServers` and `permissionMode` front-matter fields on subagents shipped in a plugin. How do we enforce the limits?

## Considered Options

* A. `tools` / `disallowedTools` allowlists and `isolation: worktree` in agent front-matter (these are honoured for plugin agents), plus one plugin `PreToolUse` hook that inspects the calling agent and blocks `Write`/`Edit` outside `specs/` for `architect`
* B. Ship agents as project files (`.claude/agents/`) copied into every repo so per-agent hooks work
* C. Prompt instructions only

## Decision Outcome

Chosen option: "A", because it keeps one distribution channel (the plugin) and uses supported fields.

### Consequences

* Good, because reviewer and test-writer limits are pure allowlists, no code.
* Bad, because the architect path rule depends on the hook input identifying the subagent; verify in Phase 2, and fall back to giving `architect` only `Write` with a hook that checks the path for every agent writing into `specs/`.

### Confirmation

Phase 2 test: `architect` asked to edit `src/` is blocked; `reviewer` has no write tool at all.

## More Information

https://code.claude.com/docs/en/sub-agents (Oct 2026).
