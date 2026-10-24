---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Session digests: a SessionEnd hook writes a short digest and posts it to a GitHub issue in council-rock

## Context and Problem Statement

The architecture says a `Stop`/`SessionEnd` hook drops a short session digest (decisions made, gotchas found) into "a queue — a branch or an issue, not the main wiki", and the curator reads the digests when the PR merges. Which hook, how is the digest written, and which queue?

## Decision Drivers

* `Stop` fires after every turn; a digest per turn is noise and cost
* `SessionEnd` hooks share a 1.5 s budget by default (raisable to 60 s) and can't block, so slow work must run detached
* Engineers have read access to `council-rock` but must not have write access to its contents
* Digests may contain secrets pasted into a session
* The curator must find a PR's digests deterministically

## Considered Options

* Queue: A. one GitHub issue per (repo, branch) in `council-rock`, labelled `session-digest`, one comment per session; B. a `digests` branch in `council-rock`; C. a file committed in the feature repo (`.digests/`); D. comments on the feature PR
* Writer: 1. `claude -p` with a small model over the transcript; 2. a `prompt`/`agent`-type hook; 3. no model, copy the last assistant message

## Decision Outcome

Chosen options: "A" + "1". A `SessionEnd` hook (matcher: `prompt_input_exit|logout|other`) starts a detached script that:

1. reads the transcript at `transcript_path`, keeps the last ~40 KB;
2. runs `claude -p --model claude-haiku-5-5 --max-turns 1 --tools ""` with a fixed digest prompt (≤ 10 bullets: decisions, gotchas, follow-ups; "nothing worth keeping" is a valid answer and posts nothing);
3. scrubs the output with `gitleaks` (existing tool) and drops it on any finding;
4. spools to `${CLAUDE_PLUGIN_DATA}/digests/` and posts with `gh issue comment` to the issue titled `digest: <owner>/<repo>@<branch>` (created if missing). Spooled files retry at the next session start if offline.

A guard env var (`BAGEERA_DIGEST_CHILD=1`) stops the inner `claude -p` from triggering its own digest.

### Consequences

* Good, because anyone with read access can open and comment on issues, so no wiki write access is handed out.
* Good, because the curator looks up the issue by repo + head branch of the merged PR, reads it, links it in `raw/`, and closes it.
* Bad, because the inner `claude -p` is a non-interactive run, billed as API usage, not seat usage. Haiku with ~40 KB input keeps it small, but it is a per-session cost line to watch in Layer 4.
* Bad, because digests are visible to everyone who can read `council-rock`. That is intended (org knowledge), and gitleaks plus "nothing worth keeping" limit what lands there; teams with sensitive repos can turn digests off with a plugin option.
* Bad, because it needs `gh` authenticated on engineers' machines. Without it, digests stay spooled locally and a warning shows once.

### Confirmation

Phase 1 tests T1.6–T1.7: ending a session on a sample branch produces one issue comment within 60 s; a transcript containing a fake AWS key produces no comment; a trivial session produces none.

## Pros and Cons of the Options

### B. Branch in council-rock

* Bad, because 100 engineers would need push rights to the wiki repo and would race on one branch.

### C. File in the feature repo

* Good, because travels with the PR.
* Bad, because it pollutes every service repo and every PR diff.

### D. Comments on the feature PR

* Good, because reviewers see them.
* Bad, because the PR often doesn't exist yet when the session ends.

### Writer 2. prompt/agent hook

* Bad, because those hook types return decisions to Claude Code; they aren't meant to produce a document, and `SessionEnd` has no decision control.

### Writer 3. no model

* Good, because free.
* Bad, because the last message rarely holds the decisions and gotchas.

## More Information

Hook facts checked against https://code.claude.com/docs/en/hooks (Oct 2026). Depends on GitHub and the Anthropic API.
