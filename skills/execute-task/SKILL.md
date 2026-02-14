---
name: execute-task
description: Execute an approved plan from docs/tasks with an execution-first loop, minimal user interaction, and strict evidence for each completed task.
---

# Execute Task

Run the plan to completion. Do not stop at analysis or status reporting.

## Canonical Definition

Execute-task is a contract-driven delivery loop:
- Read the approved plan contract.
- Execute one smallest concrete step.
- Verify with observable evidence.
- Update status.
- Continue until final acceptance is evaluated.

## Evidence Base (Anti-Drift, as of 2026-02)

- Long-context models can miss relevant details when information sits in the middle of context windows, so periodic re-anchoring is required (Liu et al., TACL 2023, "Lost in the Middle").
- Long-horizon SWE agents degrade with context explosion and semantic drift without active context management; structured compression improves stability under bounded context budgets (Liu et al., arXiv 2025, "Context as a Tool").
- Repository-scale coding requires cross-file context; benchmarks show large drops without retrieval of relevant repository context (RepoBench 2023; CrossCodeEval 2023; RepoCoder 2023).
- Task switching/interruptions in software engineering increase cognitive load and disrupt performance; self-interruptions are often especially costly (Abad et al., EASE 2018; Parnin and Rugaber, ICPC 2009).

## Best Default (Picked)

Use Convention-Anchored Single-Track Execution by default.
- Step A: Lock project conventions (`AGENTS.md`, nearby file patterns, plan contracts).
- Step B: Execute one task slice at a time with evidence gates.
- Step C: Re-anchor and compress context at phase boundaries, then continue.

Why this default is best:
- Minimizes drift toward generic LLM priors over long runs.
- Preserves repository consistency and maintainability.
- Reduces interruption overhead and avoids multitasking churn.
- Keeps completion claims tied to verifiable artifacts.

## Quick Path
1. Follow project conventions from `AGENTS.md` and local file patterns first.
2. Read only the target plan file in `docs/tasks/...`.
3. Execute tasks phase-by-phase until complete, blocked, or explicitly paused by user.
4. Treat `execute-plan <path>` as an alias for this same workflow.

## Goal

Deliver observable outcomes from the plan with minimal drift, minimal user interrupts, and verifiable evidence.

## Convention Lock (Required)

Before edits, lock on project-specific conventions instead of generic defaults.

- Read `AGENTS.md` plus plan-referenced docs before making changes.
- Prefer existing repository patterns over generic "best practice" advice.
- Reuse naming/style/test conventions from nearby files in the same module.
- If a convention is unclear, inspect local examples before inventing a new pattern.
- Record a short "convention checklist" in preflight and use it during execution.

## Hard Rules

1. Do not execute outside approved plan scope unless the user approves.
2. Default to autonomous execution with no routine check-ins.
3. Ask the user only when a high-risk/destructive decision is unavoidable.
4. Default mode is `single`; use `parallel` only for explicit `[NB]` signals in task descriptions with low write overlap.
5. `done` is allowed only with concrete evidence.
6. If evidence is missing, status must be `blocked` or `deferred`, never `done`.
7. Do not end after a checklist/report; continue until plan acceptance is evaluated.
8. If all acceptance criteria pass, move file `docs/tasks/...` -> `docs/done-tasks/...`.
9. If the plan includes `Task Status`, treat it as status source of truth and update only its checkboxes.
10. If a phase includes `Implementation Strategy`, follow its `Why/How/Check before/Be careful` constraints unless user-approved changes are required.
11. Treat each phase `Inputs`, `Deliverable`, and `Verify with` lines as the execution contract.
12. Project conventions from `AGENTS.md` and local code patterns override generic priors.
13. At each phase boundary, re-anchor on the plan contract and convention checklist before continuing.
14. If unsure between two approaches, pick the one that is most consistent with existing repository patterns.
15. Never stop after intermediate reporting; continue execution unless blocked or explicitly paused.
16. Keep WIP low: one active task by default unless explicit low-risk `[NB]` parallelization is requested by plan.
17. If `learn` is invoked mid-execution, save memory then resume the prior task flow in the same turn; do not wait for "continue".

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
- Model target: from plan header if specified.
- Mode: `single | parallel`.
- Constraints: stack/process/risk limits.
- Convention sources: `AGENTS.md`, plan docs, closest analogous files.
- Convention checklist: naming, structure, validation, commit/report expectations.
- First task: first unblocked task to execute.
- Assumptions (optional): safest assumption + rollback trigger.
- Contract check: `Inputs`, `Deliverable`, `Verify with` are concrete.

## Execution Loop (Required)

For each unblocked task:

1. Restate task done condition in one line plus the phase `Deliverable` and `Verify with` contract, aligned to the convention checklist.
2. Perform one concrete action (edit, command, test, migration step, or research step).
3. Verify with targeted evidence.
4. Update status: toggle the matching `Task Status` checkbox for `done`; use report labels for `blocked | deferred | in_progress`.
5. Continue to next unblocked task without waiting for user.

If a task fails twice with different fixes, mark `blocked` with smallest decision needed and continue remaining tasks.

## Drift Control (Required)

Prevent quality drift during long execution:

1. At each phase end, quickly re-read:
- current phase tasks
- `Task Status` checkboxes
- convention checklist
2. If recent changes drift from local conventions, correct drift before starting next task.
3. After interruptions/context switches, re-run preflight contract check in short form.
4. If unresolved convention ambiguity remains after local lookup, ask one precise question and continue on answer.

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
- Do not prefer abstract best practices over explicit repository conventions.
- Keep output short and decision-focused.


Do not stop or interrupt the execution loop for routine status updates; only ask the user when a high-risk decision is needed or a task is blocked with no clear unblock action otherwise process from start to finish with almost no user interaction.
