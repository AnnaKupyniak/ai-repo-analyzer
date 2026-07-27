# LLM Technical Evaluation

## Goal

Convert collected repository evidence into a structured technical review.

## Required Sections

1. Project Structure Overview
2. Core Technologies
3. Strengths
4. Potential Issues
5. Recommendations for Improvement

## Output Contract

Produce:

- `analysis` object in JSON format
- matching human-readable markdown content

## JSON Shape

```json
{
  "summary": "",
  "projectStructure": [],
  "technologies": [],
  "strengths": [],
  "issues": [],
  "recommendations": []
}
```

## Evaluation Rules

- Ground every claim in collected files/tree evidence.
- Do not invent architecture, frameworks, or tooling.
- If evidence is missing, state that explicitly.
- Keep findings concrete and technical.
- If frontend and backend both exist, evaluate them separately.
