# Context Gathering

## Goal

Collect repository context with a strict **tool-only** flow (no script generation or execution).

## Required Inputs

- `OWNER`
- `REPO`
- `GITHUB_TOKEN`

## Hard Rules

- Do not create or run any `.ps1`, `.py`, `.sh`, or scratch helper files.
- Use GitHub API tools directly for metadata/tree/file retrieval.
- Do not clone the repository.
- Stop immediately on missing token, unresolved repo, or hard API failures (401/404).

## Preferred Retrieval Order

1. `GET /repos/{owner}/{repo}` to resolve repository metadata and default branch.
2. `GET /repos/{owner}/{repo}/git/trees/{sha}?recursive=1` to get the file tree.
3. Fetch only key files for architectural evidence (README, manifests, entrypoints, config).

## Fallback

If recursive tree data is unavailable, use `/contents` traversal through API tools only.

## Rate-Limit Behavior

- Observe `X-RateLimit-Remaining`.
- On `403` limit responses, apply bounded retry/backoff.
- If retries fail, stop with a clear error.

## Outputs

- Save the retrieved file tree (list of files in the repository) to `output/structure.txt`. Ensure the directory `output/` exists before writing.
