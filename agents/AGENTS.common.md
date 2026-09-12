# Shared AI-agent conventions (isejalabs)

This file is imported into each consuming repo's own `AGENTS.md` (`CLAUDE.md` symlinked to it) via Claude Code's `@path` import syntax, after adding this repo as a git submodule. It holds conventions that are genuinely identical across multiple isejalabs repos — repo-specific conventions (architecture, domain, build commands) stay in the consuming repo's own `AGENTS.md`, never here.

## Commit messages

Follow Conventional Commits with a path-derived scope: `type(scope): subject`, e.g. `feat(module-name): ...`, `fix: ...`. Scope is typically the primary directory/module/component the change touches; omit it (just `type: subject`) when a change spans multiple areas and isn't specific to one. Don't reference an issue in the commit message itself (no `Refs #123`/`Closes #123`) — issue references (with a closing keyword where appropriate) belong in the PR description, not individual commits.

Use scope `ai` (`docs(ai): ...`) for changes to `AGENTS.md`/`CLAUDE.md` itself — instructions aimed at AI agents rather than repo documentation in general. Use `ai/<skill>` (`docs(ai/<skill>): ...`) when the change is scoped to one skill under `.agents/skills/<skill>/` (`.claude/skills` is symlinked the same way).

## PR discipline

- **Never push directly to `main`** (or any other protected/default branch) — always through a PR, regardless of size or how temporary the change is meant to be.
- **Prerequisite items**: if something must happen before an action can be taken — merging a PR, closing an issue, applying a plan-based change — never assume it's done just because it was mentioned earlier or the user said "looks good". For a PR, list it under a `## Before merging` heading in its body as a checklist; the same rule applies to a prerequisite stated in a standalone issue or plan, PR or not. Before taking the dependent action, list each item back to the user explicitly and get per-item confirmation — a general go-ahead doesn't count. This is agent-mediated: it applies whenever an agent performs the action, including via CLI — only a human bypassing the agent entirely skips it. Once an item is confirmed, check it off in the PR body as part of the same action.
- **Recording a "blocked by" relationship**: when one issue genuinely can't be worked/closed until another lands, set it up as a real GitHub issue relationship via `gh api graphql` (`addBlockedBy`, taking `issueId`/`blockingIssueId` node IDs from an `issue(number: N) { id }` query) — this only accepts Issue nodes on both sides, rejecting a PR's node ID outright. When either side is a PR, fall back to the `## Before merging` checklist convention instead.
- **Linking to another repo's issue/PR**: use a short link through `redirect.github.com` instead of a plain `github.com` link, e.g. `[owner/repo#123](https://redirect.github.com/owner/repo/pull/123)`. A plain link creates a "mentioned this pull request" cross-reference on the *target's* timeline, noise on a repo we don't own; the `redirect.github.com` form resolves the same for readers without triggering that notification.
