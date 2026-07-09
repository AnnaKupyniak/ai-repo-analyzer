---
name: ai-repo-analyzer
description: >
  Analyze a GitHub repository with a strict tool-only workflow.
  Gather remote context, perform a structured technical evaluation,
  and save output artifacts locally.
---

# GitHub Repo Analyzer

This skill is split into focused modules:

1. [01-context-gathering.md](01-context-gathering.md) for safe repository discovery and file retrieval.
2. [02-llm-evaluation.md](02-llm-evaluation.md) for the structured LLM review.
3. [03-local-storage.md](03-local-storage.md) for writing `output/analysis.json` and `output/report.md`.

Use this file only as the orchestration entry point. Follow modules in order.

## Invocation Contract

- The user may provide only a short trigger prompt in CLI.
- The skill itself must own the full workflow, including context gathering, evaluation, and artifact writing.
- The target repository must be available from the current task context or from the repository URL / owner-repo input supplied by the user.
- If the target repository is missing, stop and request only that missing value.

## Operating Rules

- **STRICT TOOL-ONLY MODE:** Do not create, write, or execute scripts (`.ps1`, `.py`, `.sh`) or scratch helper files.
- Use only available tools (GitHub API tools, file read/write tools, and skill modules).
- Keep repository context gathering API-driven (metadata, tree, selected file contents).
- Handle GitHub rate-limit responses (`403`) with retry/backoff behavior.
- The only permitted final artifacts are `output/analysis.json` and `output/report.md`.

## Stop Conditions

- Stop if `GITHUB_TOKEN` is missing or unusable.
- Stop if the repository target cannot be resolved.
- Stop if the agent cannot write `output/analysis.json` and `output/report.md`.
- Stop immediately if any step requires script generation/execution.

## Acceptance Criteria

- Remote context is collected from GitHub API responses.
- The technical review contains all five required sections.
- The local workspace contains both required output files (`output/analysis.json` and `output/report.md`).

## Execution Order

1. Collect remote context with [01-context-gathering.md](01-context-gathering.md).
2. Build the technical evaluation with [02-llm-evaluation.md](02-llm-evaluation.md).
3. Persist artifacts with [03-local-storage.md](03-local-storage.md).

## Required Final Outputs

- `output/analysis.json`
- `output/report.md`