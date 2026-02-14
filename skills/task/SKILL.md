---
name: task
description: Use when creating or refining implementation tasks for engineering work. Trigger for requests like "make a plan", "break this into tasks", "phase this work", or "prepare parallel agent execution". Produce brief, strict, self-explanatory tasks with phases, architecture-level implementation strategy notes, a per-task status checklist, blocking vs non-blocking dependencies, scope, and acceptance criteria. Do not provide step-by-step coding instructions.
---

# Task

Create brief, strict, parallel-ready execution tasks.

## Quick Path

Run `use-memory`, then create tasks.

## Goal

Produce a plan that states:
- What will be done
- How each task will be executed (approach-level)
- Which architecture constraints/choices guide execution
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
10. Every phase task line must include `Approach: <how>` in one short phrase (strategy-level, no code steps).
11. Every phase must include `Implementation Strategy (no code steps)` with 2-5 architecture-level bullets.
12. Strategy bullets must be decision-oriented and include rationale using `Do: <choice> - Because: <reason>` and, when relevant, `Avoid: <choice> - Because: <risk>`.
13. Strategy bullets may name libraries, patterns, data-shape constraints, and integration boundaries; never provide line-by-line coding instructions.
14. Add a `Task Status` section where each line is only `- [ ] <Task name>` (or `- [x] <Task name>` when done).
15. Track progress by toggling checkbox state in `Task Status`; do not rewrite task description lines.
16. Write tasks to `docs/tasks/YYYY-MM-DD-<topic>.md`.
17. Start with a one-sentence brief of plan outcome.
18. Add stack/process guidance in max 2 sentences.
19. If process constraints are known from prompt/skills, state them in the header and enforce them.
20. If user does not answer clarification, stop after the question and wait.
21. If the user asks for "best" definitions or interaction patterns, run focused online research first and cite 3-6 authoritative sources.

## LLM Clarity Rules

1. Use exact field labels from the template; do not rename sections.
2. Keep one idea per line; avoid multi-clause sentences.
3. Use concrete nouns (`API contract`, `cache layer`, `event schema`) instead of pronouns (`it`, `this`).
4. Keep each `Approach` phrase short (about 4-10 words).
5. Keep each strategy bullet to one decision plus one reason.
6. Prefer deterministic wording: `Do:` and `Avoid:` only (no synonyms).
7. If a key detail is unknown, add it under `Assumptions` instead of guessing.

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
- Write task description lines without checkboxes; keep tags: `[B]` blocking, `[NB]` non-blocking.
- Require this order in each task line: task name -> owner -> `Approach` -> `Why` -> `Benefit`.
- Add `Implementation Strategy (no code steps)` under each phase with 2-5 `Do`/`Avoid` bullets and explicit rationale.
- Add owner hint where parallel work is possible (`Agent A/B/C`).
- Mirror each task once in `Task Status` using checkbox lines with task name only.

5. Quality pass:
- Remove vague/duplicate tasks.
- Ensure each phase has a measurable done condition.
- Ensure task names match exactly between `Phases` and `Task Status`.
- Ensure `Approach` and strategy bullets remain concise and unambiguous for parsing.
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
- <Task> [B|NB] (Owner: Agent A) - Approach: <how> - Why: <reason> - Benefit: <benefit>
- Implementation Strategy (no code steps):
- Do: <architectural step/choice> - Because: <reason>
- Avoid: <pattern/constraint> - Because: <risk>
- Done when:

### Phase 2 - <Outcome>
- <Task> [B|NB] (Owner: Agent B) - Approach: <how> - Why: <reason> - Benefit: <benefit>
- Implementation Strategy (no code steps):
- Do: <architectural step/choice> - Because: <reason>
- Avoid: <pattern/constraint> - Because: <risk>
- Done when:

## Task Status
- [ ] <Task name from Phase 1>
- [ ] <Task name from Phase 2>

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

A good task description line must:
- Start with a verb.
- Name the outcome, not the implementation.
- Be independently completable by one agent.
- Include a short `Approach` phrase that explains how the outcome will be delivered.
- Include a reason and expected benefit in simple language.
- Include a checkable completion condition.

A good implementation strategy entry must:
- Describe architecture-level execution choices, not code-level actions.
- Name at least one explicit design decision and why it is preferred.
- Include an `Avoid` entry when a known anti-pattern or risk should be prevented.
- Stay concise (one line per decision with reason).

Use this strategy mini-template when possible:
- `Do: Use <library/pattern> for <boundary> - Because: <constraint or benefit>`
- `Avoid: <pattern/structure> in <boundary> - Because: <risk>`

A good task status line must:
- Use only checkbox state plus task name.
- Match exactly one task description line.
- Avoid owner/why/benefit metadata.

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
- Do not omit `Approach` in phase task lines.
- Do not omit `Implementation Strategy (no code steps)` in phases.
- Do not provide code-level recipes disguised as architecture strategy.
- Do not edit phase task text to show progress; toggle only `Task Status` checkboxes.
- Do not output a full task plan in the same turn as the first clarification question.

## Handoff

After the task plan is created and confirmed, execution can start with `execute-task` (or `execute-plan` if your environment uses that alias) using the saved plan in `docs/tasks/...`.
