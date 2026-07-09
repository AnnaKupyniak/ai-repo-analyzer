# PR Reporting & Publishing

## Goal

Save the generated code review report locally and post it to the GitHub Pull Request.

## Local Storage

1. Ensure the directory `output/` exists.
2. Save the markdown report to `output/pr_review.md`.
3. Overwrite any existing review report from previous runs.

## GitHub Integration

1. **Submit PR Comment**:
   - Use the GitHub API to post the complete review report as a comment on the PR conversation thread:
     `POST /repos/{owner}/{repo}/issues/{pull_number}/comments`
     Payload:
     ```json
     {
       "body": "...[Markdown review content]..."
     }
     ```
2. **Graceful Fallback**:
   - If the request fails with a permissions error (e.g. 403 Forbidden due to token scopes), print a warning to the console but complete the task successfully. The local markdown report in `output/pr_review.md` will serve as the final output.
