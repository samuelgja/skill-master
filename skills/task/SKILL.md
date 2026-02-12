---
name: task
description: Use when creating or refining implementation tasks for engineering work. Trigger for requests like "make a plan", "break this into tasks", "phase this work", or "prepare parallel agent execution". Produce brief, strict, self-explanatory tasks with phases, checkboxes, blocking vs non-blocking dependencies, scope, and acceptance criteria. Do not provide step-by-step coding instructions.
---

# Task

Create brief, strict, parallel-ready execution tasks.

## Quick Path

Run `use-memory`, then create tasks.

## Goal

Produce a plan that states:
- What will be done
- Why it matters
- Scope boundaries
- How success is verified

## Non-Negotiables

1. Keep tasks concise. No implementation-level code instructions.
2. Ask exactly 1 decision-shaping clarification question before drafting, unless user says to skip questions.
3. Clarification must be a short pick-list with 2-4 options plus `Other`.
4. For best UX, present clarification options in checkbox style.
5. Ask the user to answer using `1`, `2`, `3`, or their own one-line option.
6. If assumptions are required, list them and ask for quick confirmation before writing the final plan.
7. Prefer phases that can be executed in parallel when safe.
8. Mark dependencies explicitly as blocking or non-blocking.
9. Every task must be testable and self-explanatory.
10. Write tasks to `docs/tasks/YYYY-MM-DD-<topic>.md`.
11. Start with a one-sentence brief of plan outcome.
12. Add stack/process guidance in max 2 sentences.
13. If process constraints are known from prompt/skills, state them in the header and enforce them.
14. If user does not answer clarification, stop after the question and wait.
15. If the user asks for "best" definitions or interaction patterns, run focused online research first and cite 3-6 authoritative sources.

## Workflow

1. Context lock:
- Restate objective, constraints, and risk/priority.
- Extract project rules and process limits.

2. Research-first gate (conditional):
- Trigger only when user requests "best practices", "research", or quality benchmarking.
- Use authoritative sources (standards, major labs, established UX/PM references).
- Summarize findings into 3-6 short rules that shape the plan.

3. Clarification gate (mandatory):
- Ask 1 high-impact question in pick-list format.
- Prefer missing info order: success criteria -> constraints -> non-goals -> deadline.
- Ask user to reply with `1`, `2`, `3`, or their own one-line answer.
- If answer is still blocking, ask 1 follow-up question; otherwise draft.

4. Draft phased plan:
- Group by outcomes, not files.
- Use checkboxes and tags: `[B]` blocking, `[NB]` non-blocking.
- Add owner hint where parallel work is possible (`Agent A/B/C`).

5. Quality pass:
- Remove vague/duplicate tasks.
- Ensure each phase has a measurable done condition.
- Add a validation section with concrete checks.

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
- Research basis: <only if research-first gate triggered>

## Scope
- In:
- Out:

## Assumptions
- <assumption>

## Clarifications
- Question:
- Pick one (checkbox style, also some description):
- Reply format: `1 | 2 | 3 | your own: <one line>`
- Answer:
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

## Task Quality Standard

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

Also validate with SCORE-lite:
- Specific: boundaries are explicit.
- Checkable: acceptance is observable.
- Ordered: dependency type is explicit.
- Recoverable: risky work has mitigation.
- Efficient: no over-decomposition.

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

After the task plan is created and confirmed, execution can start with `execute-task` (or `execute-plan` if your environment uses that alias) using the saved plan in `docs/tasks/...`.
