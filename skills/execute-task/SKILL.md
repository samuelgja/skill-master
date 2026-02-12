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
3. Confirm execution mode only if unclear:
- If user/plan already specifies mode, use it.
- If unclear, ask one question: `Single agent` or `Parallel workers`.
4. If assumptions are needed, list them and ask for confirmation.
5. Keep context lean: do not load entire docs when only one phase is needed.
6. Complete one task at a time per worker, with explicit verification.
7. Mark task status in output (`done`, `blocked`, `deferred`).
8. When the full plan is complete, move the plan file from `docs/tasks/` to `docs/done-tasks/`.

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

Use this format each update:

- Phase:
- Task:
- Status: `done | blocked | deferred | in_progress`
- Evidence:
- Next:
- Risks/Notes:

## Guardrails

- Do not rewrite the plan during execution unless user asks.
- Do not provide long narrative where a short status is enough.
- Do not repeat previous failed action without a changed hypothesis.
- Do not hide uncertainty; surface it early with a concrete unblock request.

## Final Acceptance Gate

Before marking the plan complete:

1. Re-check all plan acceptance criteria.
2. Confirm no blocking tasks remain open.
3. If any criterion is unmet, report `blocked` or `deferred` with the smallest next action.
4. If all criteria are met, move the completed plan file to `docs/done-tasks/` (keep filename unchanged).
