---
name: task
description: Use when creating or refining implementation tasks for engineering work. Trigger for requests like "make a plan", "break this into tasks", "phase this work", or "prepare parallel agent execution". Produce brief, strict, self-explanatory tasks with phases, checkboxes, blocking vs non-blocking dependencies, scope, and acceptance criteria. Do not provide step-by-step coding instructions.
---

# Task

Create PM-style execution tasks that are brief, strict, and parallel-friendly.

## Quick Path

Run `use-memory` first to load constraints/preferences, then continue with task creation.

## Goal

Produce a short plan that tells:
- What we will do
- Why it matters
- Where in the system it likely applies (component/area, not file-by-file)
- What benefit we expect

## Hard Rules

1. Keep tasks concise. No implementation-level code instructions.
2. Always ask clarification before drafting v1 unless the user explicitly says to skip questions.
3. Ask exactly 1 high-impact clarification question first; ask a 2nd only if the first answer leaves a blocking ambiguity.
4. If assumptions are required, list them and ask for quick confirmation before writing the final plan.
5. Prefer phases that can be executed in parallel when safe.
6. Mark dependencies explicitly as blocking or non-blocking.
7. Every task must be testable and self-explanatory.
8. Write tasks to `docs/tasks/YYYY-MM-DD-<topic>.md`.
9. Start every plan with a one-sentence brief: what this plan delivers.
10. Add stack/process guidance in max 2 sentences (language/framework/tools and workflow rules from prompt or loaded skills).
11. If prompt/skills define process constraints (example: not using git), mention and enforce them in the header.
12. Do not start execution-oriented wording ("implement", "run now", "apply immediately") before clarification is complete.
13. If user does not answer clarification, stop after posting the single question and wait.

## Planning Process

1. Context lock:
- Restate objective, constraints, and deadline/risk level.
- Note relevant project rules/skills to respect in one short header line.
- Extract stack/process constraints from prompt and loaded skills before drafting phases.

2. Clarification gate (mandatory):
- Ask 1 question that changes scope, sequencing, or acceptance criteria.
- Prefer missing info in this order: success criteria -> constraints -> non-goals -> deadline/priority.
- Do not draft phases yet.
- If user says "skip questions", proceed with explicit assumptions.
- If user does not respond, wait instead of auto-planning.

3. Optional second clarification:
- Ask 1 additional question only if still blocked by a major ambiguity.
- Otherwise proceed to draft.

4. Draft phased plan:
- Group work by outcome, not by files.
- Use short task lines with checkboxes.
- Add dependency tag per task: `[B]` blocking, `[NB]` non-blocking.
- Add owner lane hint when parallelization is possible (Agent A/B/C).

5. Quality pass:
- Remove vague tasks.
- Remove duplicate tasks.
- Ensure each phase has a clear done condition.
- Run a DoR-lite check on each blocking task: clear outcome, owner, dependency, measurable done condition.
- Add one "Validation" task or section that verifies behavior with concrete checks.

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

## Clarifications
- Q1:
- A1:
- Status: `answered | user_skipped_questions`

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

Also validate each task against 2026 agent-planning quality signals:
- Specific: objective and boundaries are explicit.
- Observable: acceptance can be verified without guessing intent.
- Sequenced: dependency type is explicit.
- Recoverable: high-risk work includes rollback/mitigation note.
- Cost-aware: avoid over-decomposition for trivial scope.

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
- Do not output a full task plan in the same turn as the first clarification question.

## Handoff

After the task plan is created and confirmed, execution can start immediately with the `execute-task` skill using the saved plan in `docs/tasks/...`.
