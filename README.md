# AI Repository Analyzer (CLI Stage)

An AI-powered agent skill that connects directly to the GitHub REST API to fetch and display the remote file structure of any repository. Developed as part of the AI-Native track.

---

## Features
- **GitHub REST API Integration:** Fetches repository file trees directly from GitHub servers without cloning or downloading the project locally.
- **Secure Token Authentication:** Uses GitHub Personal Access Tokens (PAT) loaded via system environment variables.
- **Recursive Directory Mapping:** Retrieves the full deep layout of the repository via recursive API tree traversal.
- **File Structure Export:** Saves the retrieved file structure directly to `output/structure.txt`.

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

## Running the Agent Skill

This project is orchestrated using the agent skill defined in `.agents/skills/ai-repo-analyzer`.
The agent executes the workflow starting from [SKILL.md](.agents/skills/ai-repo-analyzer/SKILL.md) and follows [01-context-gathering.md](.agents/skills/ai-repo-analyzer/01-context-gathering.md) to gather the files and write them to `output/structure.txt`.