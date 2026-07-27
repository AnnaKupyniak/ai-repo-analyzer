# Local Storage

## Goal

Persist analysis artifacts locally using built-in tools only.

## Required Outputs

- `output/analysis.json`
- `output/report.md`

## Write Order

1. Ensure `output/` exists.
2. Write `analysis.json`.
3. Write `report.md`.

## Rules

- Keep JSON and markdown synchronized from the same evaluation run.
- Overwrite stale artifacts from prior runs.
- Use UTF-8 encoding.
- Do not use external scripts or ad hoc helper files for writing outputs.
