# AI Repository Analyzer

An AI-powered agent skill that connects directly to the GitHub REST API to fetch and display the remote file structure of any repository, and performs a structured technical evaluation using LLMs. Developed as part of the AI-Native track.

---

## Features
- **GitHub REST API Integration:** Fetches repository file trees directly from GitHub servers without cloning or downloading the project locally.
- **Secure Token Authentication:** Uses GitHub Personal Access Tokens (PAT) loaded via system environment variables.
- **Recursive Directory Mapping:** Retrieves the full deep layout of the repository via recursive API tree traversal.
- **File Structure Export:** Saves the retrieved file structure directly to `output/structure.txt`.
- **LLM-Powered Repository Evaluation:** Analyzes codebase architecture, core technologies, strengths, issues, and improvement recommendations using advanced LLM integration.
- **Local Report Generation:** Automatically saves a structured `analysis.json` and a human-readable `report.md` under the `output/` directory.

---

## How to Set Up

### 1. Set Up Your GitHub Token
Before running the agent skill, you must set your GitHub Personal Access Token (PAT) as an environment variable in your terminal:

**In PowerShell (Windows):**
```powershell
$env:GITHUB_TOKEN="your_github_personal_access_token_here"
```

**In Bash (macOS/Linux):**
```bash
export GITHUB_TOKEN="your_github_personal_access_token_here"
```

---

## Running the Agent Skills

This project contains two orchestrated agent skills located in `.agents/skills/`.

### 1. Repository Analyzer (`ai-repo-analyzer`)
Orchestrated starting from [SKILL.md](.agents/skills/ai-repo-analyzer/SKILL.md):
1. [01-context-gathering.md](.agents/skills/ai-repo-analyzer/01-context-gathering.md) — Gathers repository layout and saves it to `output/structure.txt`.
2. [02-llm-evaluation.md](.agents/skills/ai-repo-analyzer/02-llm-evaluation.md) — Connects to LLM and performs structured evaluation.
3. [03-local-storage.md](.agents/skills/ai-repo-analyzer/03-local-storage.md) — Writes reports to `output/analysis.json` and `output/report.md`.

### 2. Pull Request Reviewer (`github-pr-reviewer`)
Orchestrated starting from [SKILL.md](.agents/skills/github-pr-reviewer/SKILL.md):
1. [01-pr-context.md](.agents/skills/github-pr-reviewer/01-pr-context.md) — Fetches file changes and patches for a specified Pull Request.
2. [02-pr-evaluation.md](.agents/skills/github-pr-reviewer/02-pr-evaluation.md) — Uses LLM to evaluate changes for bugs, style, and optimizations.
3. [03-pr-reporting.md](.agents/skills/github-pr-reviewer/03-pr-reporting.md) — Saves the review to `output/pr_review.md` and posts a review comment directly to the GitHub PR.