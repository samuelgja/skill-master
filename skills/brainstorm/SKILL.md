---
name: brainstorm
description: Use for ideation and spec-definition work before implementation. Trigger when the user asks to explore ideas, compare approaches, clarify requirements, or shape a design direction. Do not trigger for straightforward implementation-only or bug-fix-only tasks.
---

# Brainstorm

Run a short, strict facilitation flow that separates idea exploration from design specification.

## Preflight

Run `use-memory` first, then carry only relevant constraints/preferences into this workflow.

## Modes

- Ideation mode: Explore and narrow options without forcing a full design.
- Design mode: Produce an implementation-ready spec only when requested or after a direction is chosen.

## Rules

1. Ask exactly one question per turn.
2. Ask only questions that change a decision.
3. Ground in current context first (files, docs, state) before deep ideation.
4. Keep answers concrete, concise, and testable.
5. Do divergence before convergence.
6. Do not force design output unless requested.
7. Keep outputs short and decision-focused.

## Workflow

1. Ground context:
- Summarize relevant current state.
- Confirm goal, constraints, and success criteria.
- Include only the memory constraints/preferences that affect the current decision.

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
