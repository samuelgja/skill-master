---
name: task
description: Use when creating or refining implementation tasks for engineering work. Trigger for requests like "make a plan", "break this into tasks", "phase this work", or "prepare parallel agent execution". Produce brief, strict, self-explanatory tasks with phases, checkboxes, blocking vs non-blocking dependencies, scope, and acceptance criteria. Do not provide step-by-step coding instructions.
---

# Task

Create PM-style execution tasks that are brief, strict, and parallel-friendly.

## Goal

Produce a short plan that tells:
- What we will do
- Why it matters
- Where in the system it likely applies (component/area, not file-by-file)
- What benefit we expect

## Hard Rules

1. Keep tasks concise. No implementation-level code instructions.
2. Ask clarification questions only when they change scope or sequencing.
3. Ask at most 2 clarification questions before drafting v1.
4. If assumptions are required, list them and ask for quick confirmation.
5. Prefer phases that can be executed in parallel when safe.
6. Mark dependencies explicitly as blocking or non-blocking.
7. Every task must be testable and self-explanatory.
8. Write tasks to `docs/tasks/YYYY-MM-DD-<topic>.md`.
9. Start every plan with a one-sentence brief: what this plan delivers.
10. Add stack/process guidance in max 2 sentences (language/framework/tools and workflow rules from prompt or loaded skills).
11. If prompt/skills define process constraints (example: not using git), mention and enforce them in the header.

## Planning Process

1. Context lock:
- Restate objective, constraints, and deadline/risk level.
- Note relevant project rules/skills to respect in one short header line.
- Extract stack/process constraints from prompt and loaded skills before drafting phases.

2. Minimal clarification:
- Ask 0-2 high-impact questions only.
- If no response yet, proceed with explicit assumptions.

3. Draft phased plan:
- Group work by outcome, not by files.
- Use short task lines with checkboxes.
- Add dependency tag per task: `[B]` blocking, `[NB]` non-blocking.
- Add owner lane hint when parallelization is possible (Agent A/B/C).

4. Quality pass:
- Remove vague tasks.
- Remove duplicate tasks.
- Ensure each phase has a clear done condition.

## Output Format (Strict)

Use this exact structure:

```md
# <Title>

## Header
- Brief:
- Objective:
- Why now:
- Constraints:
- Stack and process guidance:
- Must-respect project rules/skills:

## Scope
- In:
- Out:

## Assumptions
- <assumption>

## Phases
### Phase 1 - <Outcome>
- [ ] [B|NB] <Task> (Owner: Agent A) - Why: <reason> - Benefit: <benefit>
- Done when:

### Phase 2 - <Outcome>
- [ ] [B|NB] <Task> (Owner: Agent B) - Why: <reason> - Benefit: <benefit>
- Done when:

## Dependency Map
- <Task/Phase> -> <Task/Phase> [B]
- <Task/Phase> -> <Task/Phase> [NB]

## Risks and Mitigations
- Risk:
- Mitigation:

## Acceptance Criteria
- [ ] <observable outcome>
- [ ] <observable outcome>
```

## Task Writing Standard

A good task line must:
- Start with a verb.
- Name the outcome, not the implementation.
- Be independently completable by one agent.
- Include a reason and expected benefit in simple language.
- Include a checkable completion condition.

Use an INVEST-lite check before finalizing:
- Independent
- Valuable
- Small
- Testable

Reject tasks like:
- "Refactor code"
- "Fix stuff"
- "Update files"

Prefer:
- "Define input validation boundaries for order intake (Owner: Agent A) - Why: prevent invalid writes - Benefit: fewer runtime failures"

## Parallel Work Rules

1. Default to parallel only when tasks do not share a critical write path.
2. If tasks may collide, split by domain boundary first, then by sequence.
3. Reserve one integration task at the end of each parallel batch.
4. Keep non-blocking tasks visible so idle agents can pick them up.

## Behavior Guardrails

- Do not force deep technical detail if user asked for a brief plan.
- Do not assume fixed files will remain fixed.
- Do not ask many questions in one turn.
- Do not proceed silently on major assumptions; ask for confirmation.
- Do not omit stack/process context when it is known from prompt or loaded skills.

## Handoff

After the task plan is created and confirmed, execution can start immediately with the `execute-task` skill using the saved plan in `docs/tasks/...`.
