# PR Context Gathering

## Goal

Retrieve the Pull Request details and the file changes (diffs/patches) using the GitHub API without cloning the repository.

## Required Inputs

- `OWNER`
- `REPO`
- `GITHUB_TOKEN`
- `PULL_NUMBER` (Optional, if missing resolve automatically)

## Retrieval Process

1. **Resolve Pull Request Number**:
   - If `PULL_NUMBER` is not explicitly provided, fetch the open pull requests using:
     `GET /repos/{owner}/{repo}/pulls?state=open`
   - Use the latest open pull request. If no open pull requests exist, stop and inform the user.
2. **Fetch PR Changed Files**:
   - Query the PR files endpoint:
     `GET /repos/{owner}/{repo}/pulls/{pull_number}/files`
   - From each file entry, extract:
     - `filename`: The path of the file.
     - `status`: `added`, `modified`, or `removed`.
     - `additions`, `deletions`: Change count.
     - `patch`: The diff chunk representing the changes.
3. **Handle Large PRs**:
   - Focus on source code files. Skip binary files, lockfiles, or files without patches.

## Rules

- Strict **tool-only** flow (no scripts or scratch files).
- Observe GitHub API rate limits.
