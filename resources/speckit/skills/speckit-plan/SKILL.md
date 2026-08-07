---
name: "speckit-plan"
description: "Create a concise implementation plan from the current feature spec."
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "template-tarea-ai-starter-kit"
  source: ".specify/templates/overrides/plan-template.md"
---

## User Input

```text
$ARGUMENTS
```

## Goal

Create one `plan.md` with the technical approach, files, tasks, validation, risks, and out-of-scope. Do not create `research.md`, `data-model.md`, `quickstart.md`, `contracts/`, or `tasks.md`.

## Workflow

1. Read `.specify/feature.json` to find the active feature directory.
2. Read `spec.md` from that directory.
3. If `spec.md` has unresolved `[NEEDS CLARIFICATION: ...]` markers, ask the user to resolve them before planning.
4. Read `AGENTS.md`, `README.md`, and only the repo files needed to understand the change.
5. Identify implementation gaps after reading the repo.
6. If a gap changes architecture, data model, security, validation, or file scope, ask the user up to 3 concise questions before writing the plan.
7. If a gap has a safe default, document it under `Riesgos` or `Fuera de alcance` instead of asking.
8. Copy the active Spec Kit template resolved from `.specify/templates/overrides/plan-template.md` into `plan.md`.
9. Fill the plan with:
   - Summary
   - Repo reading notes
   - Exact files likely to change
   - Small implementation steps
   - Embedded task checklist
   - Validation commands or manual checks
   - Risks and out-of-scope
10. Keep implementation tasks small enough to review in one PR.

## Quality Bar

- The plan is implementation-oriented and grounded in current repo files.
- Every task mentions a concrete file or behavior.
- Validation is runnable or manually checkable.
- Critical implementation gaps were asked or documented.
- No extra Spec Kit artifacts are generated.

## Done When

- `specs/YYYYMMDD-HHMMSS-short-name/plan.md` exists.
- The plan has a task checklist and validation section.
- Report the plan path and recommended next action.
