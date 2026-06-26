# gh-pr-review-thread-resolver agent instructions

Use this repository to maintain the owned GitHub CLI extension `gh-pr-review-thread-resolver`.

## Scope

- Keep the repo minimal: `AGENTS.md`, `README.md`, the executable extension script, and normal git metadata.
- Do not add tool-specific project directories such as `.github/`, `.roo/`, `.pi/`, `.agents/`, `.claude/`, or `.cursor/` unless the user explicitly asks.
- Keep reusable workflow guidance in the global `github-extensions` skill, not in repo-local skill directories.

## Commands

```sh
bash -n gh-pr-review-thread-resolver
shellcheck gh-pr-review-thread-resolver
gh pr-review-thread-resolver --help
```

## Extension behavior

- `list` shows inline PR review threads, not top-level PR comments.
- `list --json` exposes review thread GraphQL node IDs, `isOutdated`, permissions, and comment pagination metadata.
- `comments.nodes` is a flat thread conversation: parent inline comment plus replies.
- When `comments.pageInfo.hasNextPage` is true, the output must clearly say more replies were not fetched.
- `resolve` and `unresolve` take review thread GraphQL node IDs, not numeric REST comment IDs.
