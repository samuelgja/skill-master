---
name: execute-task
description: Execute an approved plan from docs/tasks with an execution-first loop, minimal user interaction, and strict evidence for each completed task.
---

# Execute Task

Run the plan to completion. Do not stop at analysis or status reporting.

## Quick Path

2. Read only the target plan file in `docs/tasks/...`.
3. Execute tasks phase-by-phase until complete, blocked, or explicitly paused by user.
4. Treat `execute-plan <path>` as an alias for this same workflow.

## Goal

Deliver observable outcomes from the plan with minimal drift, minimal user interrupts, and verifiable evidence.

## Hard Rules

1. Do not execute outside approved plan scope unless the user approves.
2. Default to autonomous execution with no routine check-ins.
3. Ask the user only when a high-risk/destructive decision is unavoidable.
4. Default mode is `single`; use `parallel` only for explicit `[NB]` signals in task descriptions or dependency map with low write overlap.
5. `done` is allowed only with concrete evidence.
6. If evidence is missing, status must be `blocked` or `deferred`, never `done`.
7. Do not end after a checklist/report; continue until plan acceptance is evaluated.
8. If all acceptance criteria pass, move file `docs/tasks/...` -> `docs/done-tasks/...`.
9. If the plan includes `Task Status`, treat it as status source of truth and update only its checkboxes.

## Research-First Gate (Conditional)

Run this gate only if the plan/user asks for "research", "best practice", "latest", or external comparisons.

- Gather 2-5 authoritative sources before edits.
- Prefer official docs/research papers over secondary summaries.
- Summarize findings as actionable constraints.
- Cite sources in the final report.
- If research is blocked, mark impacted tasks `blocked` and continue other in-scope tasks.

## Preflight (Required)

Capture this brief before phase execution:

- What: exact plan outcome.
- Why: user/business impact.
- Mode: `single | parallel`.
- Constraints: stack/process/risk limits.
- First task: first unblocked task to execute.
- Assumptions (optional): safest assumption + rollback trigger.

## Execution Loop (Required)

For each unblocked task:

1. Restate task done condition in one line.
2. Perform one concrete action (edit, command, test, migration step, or research step).
3. Verify with targeted evidence.
4. Update status: toggle the matching `Task Status` checkbox for `done`; use report labels for `blocked | deferred | in_progress`.
5. Continue to next unblocked task without waiting for user.

If a task fails twice with different fixes, mark `blocked` with smallest decision needed and continue remaining tasks.

## Evidence Standard

A task can be `done` only if at least one is present:

- File evidence: changed path(s) and what changed.
- Command evidence: command + key result.
- Behavior evidence: test/check proving done condition.
- Research evidence: source(s) + applied decision.

Invalid evidence examples: "analyzed", "reviewed", "documented internally" with no observable artifact.

## Phase Gate

At phase end, output:

- Phase:
- Tasks completed:
- Tasks blocked/deferred:
- Evidence summary:
- Can proceed: `yes | no` with reason.

If `no`, attempt one safe unblock action, then continue with remaining phases if still blocked.

## Final Acceptance Gate

1. Re-check all plan acceptance criteria.
2. If any criterion is unmet, report exact criterion as `blocked`/`deferred` plus next smallest action.
3. If all criteria are met, move plan file to `docs/done-tasks/`.
4. Publish one concise final execution report with:
- Per-phase status
- Evidence by task
- Deferred/blocked items
- Assumptions used
- Research sources (if research gate ran)

## Guardrails

- Do not rewrite the plan unless user asks.
- Do not rewrite phase task lines for status tracking; update `Task Status` checkboxes instead.
- Do not report completion without execution artifacts.
- Do not repeat the same failed action without a changed hypothesis.
- Keep output short and decision-focused.
