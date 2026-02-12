---
name: execute-task
description: Use to execute an approved task plan from docs/tasks. Trigger when user asks to implement a plan, run phases, or execute tasks with one agent or multiple agents. Start with a strict preflight (context, constraints, stack, required skills), then execute phase-by-phase with progress evidence and completion checks.
---

# Execute Task

Execute tasks fast without drifting from scope.

## Quick Path

Run `use-memory` first to load constraints/preferences, then continue with execution.

## Goal

Turn a plan into completed work with minimal mistakes, minimal repetition, and clear evidence.

## Why agents fail (and what to do)

- Failure pattern: instruction drift in long context.
  - Control: read only required plan slices and keep an active "current phase/task" focus.
- Failure pattern: vague instructions cause repeated mistakes.
  - Control: restate exact task outcome, constraints, and done condition before editing.
- Failure pattern: no feedback loop.
  - Control: after each task, run checks and self-correct before moving on.

## Hard Rules

1. Do not execute before preflight is complete.
2. Do not run tasks outside the approved plan scope unless user approves.
3. Default to autonomous execution with no routine user interaction.
4. Default mode is `single` (single manager lane). Use `parallel` only for explicit non-blocking, low-overlap lanes.
5. If assumptions are needed, list them, choose the safest actionable assumption, and continue.
6. Do not stop between phases. Continue autonomously through all phases and return only once at the end.
7. If a risk gate is hit, do not ask immediately; take the safest non-destructive fallback, mark affected items `deferred` or `blocked`, and continue remaining in-scope work.
8. Keep context lean: do not load entire docs when only one phase is needed.
9. Complete one task at a time per worker, with explicit verification.
10. Mark task status in output (`done`, `blocked`, `deferred`).
11. When the full plan is complete, move the plan file from `docs/tasks/` to `docs/done-tasks/`.

## Preflight (Required)

1. Read target plan file in `docs/tasks/...`.
2. Extract only:
- brief/objective
- current phase
- active tasks
- acceptance criteria
- blocking dependencies
- non-blocking dependencies
- potential shared-write collision points
3. Detect stack and workflow constraints from:
- root config/manifests (package/tooling files)
- prompt constraints from user
- loaded skills that define process/style constraints
4. Output a 5-line execution brief:
- what
- why
- mode (`single` or `parallel`)
- constraints to respect
- first task to execute
5. Output an assumptions log if needed:
- assumption
- why it is safe enough to proceed
- rollback trigger

## Phase Start Checklist (Required Before Each Phase)

Before executing tasks in a phase, record this checklist internally:

- Phase:
- Have:
- Missing:
- Todo this phase:
- [ ] Task 1
- [ ] Task 2
- Can proceed: `yes | no` (one-line reason)

At final response, include all phase checklists in order.

Rules:
- Keep checklist concrete and short.
- If `Can proceed: no`, try one autonomous unblock action first.
- If still blocked, mark only the impacted tasks `blocked` or `deferred`, then continue the rest of the plan.

## Execution Mode

### Single agent

Use when tasks are tightly coupled or high-risk.

Flow:
1. Execute highest-priority unblocked task.
2. Verify with targeted checks.
3. Update status and move to next unblocked task.

### Parallel workers

Use when tasks are marked non-blocking and have low overlap.

Flow:
1. Assign one task lane per worker from `[NB]` tasks.
2. Keep one integration lane for cross-task merge/verification.
3. Re-sync after each completed task batch.
4. If collision risk appears, pause that lane and re-sequence.

## Anti-Repetition Loop (after each task)

1. Compare result vs done condition.
2. If mismatch, state why in one sentence.
3. Apply one corrective action.
4. Re-verify.
5. If still failing after 2 corrections, mark `blocked` and report smallest decision needed.

## Output Format

Use this format internally during execution, and publish one consolidated report at the end:

- Phase:
- Task:
- Status: `done | blocked | deferred | in_progress`
- Evidence:
- Next:
- Risks/Notes:
- Assumptions used (if any):

## Guardrails

- Do not rewrite the plan during execution unless user asks.
- Do not provide long narrative where a short status is enough.
- Do not repeat previous failed action without a changed hypothesis.
- Do not hide uncertainty; surface it early with a concrete unblock request.
- Do not ask the user between phases.

## Final Acceptance Gate

Before marking the plan complete:

1. Re-check all plan acceptance criteria.
2. Confirm no blocking tasks remain open.
3. If any criterion is unmet, report `blocked` or `deferred` with the smallest next action.
4. If all criteria are met, move the completed plan file to `docs/done-tasks/` (keep filename unchanged).
