# ci-privileged

Privileged CI operations for [twentyhq/twenty](https://github.com/twentyhq/twenty).

This repo isolates write tokens from the main twenty repository. Workflows here handle PR comment posting, cross-repo responses, and other operations that require elevated permissions. No contributor code is ever executed in this repo.

## Security model

- **twentyhq/twenty** holds only CI/build secrets and a minimal dispatch token scoped to this repo
- **This repo** holds tokens that grant write access to twenty PRs and core-team-issues
- All changes require CODEOWNERS approval before merge
- Workflows only receive data via `repository_dispatch` and perform simple, auditable operations (posting comments, downloading artifacts)

## Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `post-preview-comment.yaml` | `repository_dispatch: preview-env-url` | Polls twenty's keepalive workflow for the tunnel URL artifact, posts it as a PR comment |
| `post-breaking-changes-comment.yaml` | `repository_dispatch: breaking-changes-report` | Downloads breaking changes analysis artifacts from twenty, posts report as a PR comment |
| `post-claude-response.yaml` | `repository_dispatch: claude-cross-repo-response` | Posts Claude's response comment on the source issue in core-team-issues |

## Required secrets

| Secret | Scope | Purpose |
|--------|-------|---------|
| `TWENTY_PR_COMMENT_TOKEN` | `pull-requests: write` + `actions: read` on twentyhq/twenty | Post PR comments and download workflow artifacts |
| `CORE_TEAM_ISSUES_COMMENT_TOKEN` | Write access on twentyhq/core-team-issues | Post comments on core-team-issues |
