# gh-resolve-comments

A small GitHub CLI extension for resolving PR review threads without a third-party dependency.

GitHub exposes "Resolve conversation" for inline PR review comments through GraphQL, not through a high-level `gh pr` command. This extension wraps the official `resolveReviewThread` and `unresolveReviewThread` mutations.

## Install

From this local checkout:

```sh
cd /home/pbjorklund/Projects/gh-resolve-comments
gh extension install .
```

After publishing to GitHub, install with:

```sh
gh extension install pbjorklund/gh-resolve-comments
```

## Usage

List unresolved review threads for the current branch's PR:

```sh
gh resolve-comments list
```

List unresolved review threads for a specific PR:

```sh
gh resolve-comments list 123 -R OWNER/REPO
```

Resolve a thread:

```sh
gh resolve-comments resolve PRRT_...
```

Unresolve a thread:

```sh
gh resolve-comments unresolve PRRT_...
```

Return raw JSON:

```sh
gh resolve-comments list 123 -R OWNER/REPO --json
```

Include already-resolved threads:

```sh
gh resolve-comments list 123 -R OWNER/REPO --all
```

## Notes

- Use the thread ID from `list`, not a numeric REST comment ID.
- Top-level PR comments do not have a resolved state. Only inline review threads do.
- `list` currently reads the first 100 review threads.
- Requires `gh` auth with pull request read/write access for private repos and resolving.

## Development

```sh
bash -n gh-resolve-comments
./gh-resolve-comments --help
```
