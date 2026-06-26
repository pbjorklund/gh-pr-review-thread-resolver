---
name: gh-pr-review-thread-resolver
description: Uses this trusted GitHub CLI extension to list, resolve, and unresolve GitHub PR inline review threads. Use when handling PR review comments, resolving or unresolving GitHub review conversations, working through reviewer feedback, or avoiding untrusted third-party gh extensions.
---

# GitHub PR review thread resolver

Use this repo-owned `gh pr-review-thread-resolver` extension for inline PR review threads. Prefer it over untrusted third-party extensions for review-thread resolving.

## Commands

```sh
gh pr-review-thread-resolver list [<pr>] [-R OWNER/REPO]
gh pr-review-thread-resolver list [<pr>] [-R OWNER/REPO] --not-outdated
gh pr-review-thread-resolver list [<pr>] [-R OWNER/REPO] --json
gh pr-review-thread-resolver resolve <thread-id>
gh pr-review-thread-resolver unresolve <thread-id>
```

## Rules

- Use `list --json` before resolving when you need machine-readable thread state.
- Use `--not-outdated` when planning code changes, so stale diff comments do not drive current edits.
- Use the review thread `id` from `list`, not a numeric REST comment ID.
- Treat a thread as a flat comment list: the parent inline comment and replies are in `comments.nodes`.
- Check `comments.pageInfo.hasNextPage`. When true, `comments.nodes` is truncated and the agent must say more replies were not fetched.
- Resolve only inline review threads. Top-level PR comments do not have a resolved state.
- Resolve after the fix or answer is posted and any relevant validation has run.
- If `viewerCanResolve` is false, report the permission issue instead of retrying.

## Normal agent loop

1. Run `gh pr-review-thread-resolver list <pr> -R OWNER/REPO --not-outdated --json`.
2. Read each thread's `path`, `line`, `isOutdated`, `comments.totalCount`, `comments.pageInfo.hasNextPage`, `comments.nodes`, and `viewerCanResolve`.
3. If `comments.pageInfo.hasNextPage` is true, state that only the fetched comments were considered and more replies exist.
4. Fix or answer one thread at a time.
5. Reply when the reviewer needs context.
6. Run the narrowest useful validation.
7. Run `gh pr-review-thread-resolver resolve <thread-id>` only for threads that are actually addressed.
