---
name: ai-repo-analyzer
description: >
  Gather remote context from a GitHub repository.
---

# GitHub Repo Analyzer

This skill modules:

1. [01-context-gathering.md](01-context-gathering.md) for safe repository discovery and file retrieval.

Use this file only as the orchestration entry point. Follow modules in order.

## Invocation Contract

- The user may provide only a short trigger prompt in CLI.
- The target repository must be available from the current task context or from the repository URL / owner-repo input supplied by the user.
- If the target repository is missing, stop and request only that missing value.

## Operating Rules

- **STRICT TOOL-ONLY MODE:** Do not create, write, or execute scripts (`.ps1`, `.py`, `.sh`) or scratch helper files.
- Use only available tools (GitHub API tools, file read/write tools, and skill modules).
- Keep repository context gathering API-driven (metadata, tree, selected file contents).
- Handle GitHub rate-limit responses (`403`) with retry/backoff behavior.

## Stop Conditions

- Stop if `GITHUB_TOKEN` is missing or unusable.
- Stop if the repository target cannot be resolved.
- Stop immediately if any step requires script generation/execution.

## Acceptance Criteria

- Remote context is collected from GitHub API responses.

## Execution Order

1. Collect remote context with [01-context-gathering.md](01-context-gathering.md).