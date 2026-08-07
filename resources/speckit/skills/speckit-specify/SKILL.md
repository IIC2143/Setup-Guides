---
name: "speckit-specify"
description: "Create a concise feature spec from a natural language request."
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "template-tarea-ai-starter-kit"
  source: ".specify/templates/overrides/spec-template.md"
---

## User Input

```text
$ARGUMENTS
```

## Goal

Turn the request into one concise `spec.md`. Do not create checklist, task, research, contract, data-model, or quickstart files.

## Workflow

1. Parse the request and extract problem, users, scope, constraints, expected result, and success criteria.
2. Identify gaps in the initial prompt.
3. If a gap materially changes scope, security, data, UX, or success criteria, ask the user up to 3 concise questions before writing the spec.
4. If a gap has a safe default, document it under `Supuestos` instead of asking.
5. Generate a short feature name in `action-noun` format, 2-4 words.
6. Create one feature directory under `specs/YYYYMMDD-HHMMSS-short-name/`, using the local creation timestamp plus the short name. Do not use sequential numeric prefixes.
7. Copy the active Spec Kit template resolved from `.specify/templates/overrides/spec-template.md` into `specs/YYYYMMDD-HHMMSS-short-name/spec.md`.
8. Fill every relevant section with concrete content.
9. Use at most 3 `[NEEDS CLARIFICATION: ...]` markers, only when the user explicitly chooses to defer an answer.
10. Write `.specify/feature.json` with:

```json
{
  "feature_directory": "specs/YYYYMMDD-HHMMSS-short-name"
}
```

## Quality Bar

- The spec describes what and why, not implementation details.
- Requirements are testable.
- Scope and out-of-scope are explicit.
- Success criteria are observable.
- Critical gaps were asked or documented as assumptions.
- No secrets or private credentials are included.

## Done When

- `specs/YYYYMMDD-HHMMSS-short-name/spec.md` exists.
- `.specify/feature.json` points to that directory.
- Report the spec path and whether planning can start.
