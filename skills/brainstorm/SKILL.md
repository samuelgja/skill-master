---
name: brainstorm
description: Use for ideation and spec-definition work before implementation. Trigger when the user asks to explore ideas, compare approaches, clarify requirements, or shape a design direction. Do not trigger for straightforward implementation-only or bug-fix-only tasks.
---

# Brainstorm

Run a short, strict facilitation flow that separates idea exploration from design specification.

## Preflight

Start brainstorming directly, carrying only constraints/preferences relevant to the decision.

## Modes

- Ideation mode: Explore and narrow options without forcing a full design.
- Design mode: Produce an implementation-ready spec only when requested or after a direction is chosen.

## Rules

1. Ask exactly one question per turn.
2. Ask only questions that change a decision.
3. Ask questions as a short pickup list, not open-ended prose.
4. Use 2-4 numbered options plus `Other`.
5. Keep option labels short (2-6 words) with one-line impact.
6. Ground in current context first (files, docs, state) before deep ideation.
7. Keep answers concrete, concise, and testable.
8. Do divergence before convergence.
9. Do not force design output unless requested.
10. Keep outputs short and decision-focused.

## Workflow

1. Ground context:
- Summarize relevant current state.
- Confirm goal, constraints, and success criteria.
- Include only the memory constraints/preferences that affect the current decision.
- If a question is needed, use:
  `Q1: <decision-shaping question>`
  `Pick one:`
  `1. <Option A> - <impact>`
  `2. <Option B> - <impact>`
  `3. Other - <one-line answer>`

2. Diverge:
- Propose 2-4 distinct approaches.
- Include tradeoffs for each (value, effort, risk).
- Lead with a recommended option and why.

3. Converge:
- Rank options against explicit criteria.
- Select one direction or identify the minimum info needed to decide.

4. Optional design handoff (Design mode only):
- Define architecture/components/interfaces.
- Define data flow, error handling, and edge cases.
- Define testing and acceptance criteria.

## Output Templates

### Ideation Summary

- Goal:
- Constraints:
- Options considered:
- Recommended option:
- Why:
- Open risks:
- Next decision:

### Design Brief (Optional)

Write only when requested:

`docs/tasks/YYYY-MM-DD-<topic>-design.md`

Include:
- Scope (in/out)
- Interfaces or APIs impacted
- Data flow
- Failure modes
- Test scenarios
- Acceptance criteria

## Anti-Patterns

- Jumping into implementation before option comparison.
- Presenting generic options with no tradeoffs.
- Over-scoping beyond stated goals.
- Forcing a full spec when the user asked to brainstorm only.
