---
name: "speckit-review"
description: "Review the current diff against the active simplified Spec Kit spec and plan."
compatibility: "Requires spec-kit project structure with .specify/ directory"
metadata:
  author: "template-tarea-ai-starter-kit"
  source: "specs/*/{spec.md,plan.md}"
---

## Goal

Review the implementation against `spec.md`, `plan.md`, and the current git diff. Prioritize bugs, missing requirements, scope creep, and missing validation.

## Workflow

1. Read `.specify/feature.json` to find the active feature directory.
2. Read `spec.md`, `plan.md`, and `AGENTS.md`.
3. Inspect `git diff`.
4. Check:
   - Requirements from `spec.md` are covered.
   - Tasks from `plan.md` are complete.
   - Changes stay inside scope.
   - Validation was run or clearly reported as missing.
   - No secrets, credentials, or unrelated edits were added.
5. Report findings first, ordered by severity.

## Output

Use this format:

```markdown
## Findings

- [severity] [file:line] Problem. Fix.

## Checks

- Ran: `<command>`
- Missing: `<command or manual check>`

## Verdict

Ready / Needs changes
```

If there are no findings, say that clearly and mention remaining validation risk.
