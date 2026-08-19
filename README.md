# gh-pr-review-thread-resolver

A small GitHub CLI extension for resolving PR review threads. The `list` command requires `jq`.

GitHub exposes "Resolve conversation" for inline PR review comments through GraphQL, not through a high-level `gh pr` command. This extension wraps the official `resolveReviewThread` and `unresolveReviewThread` mutations.

## Install

From this local checkout:

```sh
cd /home/pbjorklund/Projects/gh-pr-review-thread-resolver
gh extension install .
```

After publishing to GitHub, install with:

```sh
gh extension install pbjorklund/gh-pr-review-thread-resolver
```

## Usage

List unresolved review threads for the current branch's PR:

```sh
gh pr-review-thread-resolver list
```

List unresolved review threads for a specific PR:

```sh
gh pr-review-thread-resolver list 123 -R OWNER/REPO
```

List unresolved review threads and skip outdated diff threads:

```sh
gh pr-review-thread-resolver list 123 -R OWNER/REPO --not-outdated
```

Resolve a thread:

```sh
gh pr-review-thread-resolver resolve PRRT_...
```

Unresolve a thread:

```sh
gh pr-review-thread-resolver unresolve PRRT_...
```

Return raw JSON:

```sh
gh pr-review-thread-resolver list 123 -R OWNER/REPO --json
```

Include already-resolved threads:

```sh
gh pr-review-thread-resolver list 123 -R OWNER/REPO --all
```

## Notes

- Use the thread ID from `list`, not a numeric REST comment ID.
- Top-level PR comments do not have a resolved state. Only inline review threads do.
- GitHub review threads are not nested trees. A thread has a flat `comments.nodes` list: parent inline comment plus replies.
- `list` includes `isOutdated`, `viewerCanResolve`, `viewerCanUnresolve`, and up to 20 comments per thread in JSON output.
- Comment output includes `comments.totalCount` and `comments.pageInfo.hasNextPage`. Text output says `comments=20/23; more not shown` when a thread has more replies than were fetched.
- `--not-outdated` filters out stale diff threads. Without it, outdated unresolved threads are still shown and marked `outdated` in text output.
- `list` paginates all review threads on a PR via GraphQL cursor pagination, so PRs with more than 100 threads are fully read.
- Requires `gh` auth with pull request read/write access for private repos and resolving, plus `jq` for `list`.

## Agent usage

Agents should use this extension when handling GitHub PR review feedback:

```sh
gh pr-review-thread-resolver list 123 -R OWNER/REPO --not-outdated --json
gh pr-review-thread-resolver resolve PRRT_...
```

Repo-local agent guidance is in [`AGENTS.md`](./AGENTS.md). Reusable workflow guidance belongs in the global `github-extensions` skill, exposed at `~/.agents/skills/github-extensions/SKILL.md` on this machine.

Repository:

```text
https://github.com/pbjorklund/gh-pr-review-thread-resolver
git@github.com:pbjorklund/gh-pr-review-thread-resolver.git
```

## Development

```sh
bash -n gh-pr-review-thread-resolver
./gh-pr-review-thread-resolver --help
```
