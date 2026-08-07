---
name: "speckit-implement"
description: "Implement the current simplified Spec Kit plan with the smallest safe diff."
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "template-tarea-ai-starter-kit"
  source: "specs/*/plan.md"
---

## Goal

Implement only what the active `plan.md` describes. Do not create extra Spec Kit artifacts.

## Workflow

1. Read `.specify/feature.json` to find the active feature directory.
2. Read `spec.md`, `plan.md`, `AGENTS.md`, and the files named in the plan.
3. Confirm the plan has concrete tasks and validation.
4. Make the smallest code/doc changes needed to complete the plan.
5. Do not modify files outside the plan unless the change is required; if so, explain why.
6. Run the validation listed in `plan.md`, or the smallest available check.
7. Leave a concise completion note with changed files, checks, and risks.

## Done When

- Planned tasks are implemented.
- Validation ran or the missing validation is explicitly reported.
- No unrelated changes were introduced.
