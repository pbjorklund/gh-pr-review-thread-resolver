---
name: gh-pr-review-thread-resolver
description: Use the local GitHub CLI extension to list, resolve, and unresolve GitHub PR inline review threads. Load when handling PR review comments, resolving GitHub conversations, or working through reviewer feedback.
---

# GitHub PR review thread resolver

Use `gh pr-review-thread-resolver` for inline PR review threads.

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
- Resolve only inline review threads. Top-level PR comments do not have a resolved state.
- Resolve after the fix or answer is posted and any relevant validation has run.
- If `viewerCanResolve` is false, report the permission issue instead of retrying.

## Normal agent loop

1. Run `gh pr-review-thread-resolver list <pr> -R OWNER/REPO --not-outdated --json`.
2. Read each thread's `path`, `line`, `isOutdated`, `comments.nodes`, and `viewerCanResolve`.
3. Fix or answer one thread at a time.
4. Reply when the reviewer needs context.
5. Run the narrowest useful validation.
6. Run `gh pr-review-thread-resolver resolve <thread-id>` only for threads that are actually addressed.
