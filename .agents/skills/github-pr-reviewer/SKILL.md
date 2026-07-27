---
name: github-pr-reviewer
description: >
  Analyze a GitHub Pull Request diff with a strict tool-only workflow.
  Gather PR context, perform LLM evaluation of changes, and save review reports locally and post to GitHub.
---

# GitHub Pull Request Reviewer

This skill is split into focused modules:

1. [01-pr-context.md](01-pr-context.md) for fetching Pull Request file changes and patches.
2. [02-pr-evaluation.md](02-pr-evaluation.md) for the LLM code review evaluation.
3. [03-pr-reporting.md](03-pr-reporting.md) for saving local `output/pr_review.md` and posting comments on GitHub.

Use this file only as the orchestration entry point. Follow modules in order.

## Invocation Contract

- The target repository (`OWNER`, `REPO`) and `PULL_NUMBER` must be available from the current task context or supplied by the user.
- If the repository or PR number is missing, the skill should look up open pull requests to resolve it. If unresolved, stop and ask the user.

## Operating Rules

- **STRICT TOOL-ONLY MODE:** Do not create, write, or execute scripts (`.ps1`, `.py`, `.sh`) or scratch helper files.
- Use only available tools (GitHub API tools, file read/write tools, and skill modules).
- Handle GitHub rate-limits (403) with retry/backoff.
- The primary local artifact produced must be `output/pr_review.md`.

## Stop Conditions

- Stop if `GITHUB_TOKEN` is missing or unusable.
- Stop if the PR target cannot be resolved.
- Stop immediately if any step requires script generation/execution.

## Acceptance Criteria

- PR context and file patches are collected from GitHub API responses.
- The LLM code review is structured and covers style, bugs, optimizations, and edge cases.
- The local workspace contains `output/pr_review.md`.
- (Optional but preferred) The review report is posted as a PR comment on GitHub.

## Execution Order

1. Collect PR context with [01-pr-context.md](01-pr-context.md).
2. Build the LLM code review evaluation with [02-pr-evaluation.md](02-pr-evaluation.md).
3. Persist the report and post to GitHub with [03-pr-reporting.md](03-pr-reporting.md).

## Required Final Outputs

- `output/pr_review.md`
