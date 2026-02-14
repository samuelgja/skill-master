---
name: brainstorm
description: Use for ideation and spec-definition work before implementation. Trigger when the user asks to explore ideas, compare approaches, clarify requirements, or shape a design direction. Do not trigger for straightforward implementation-only or bug-fix-only tasks.
---

# Brainstorm

Run a research-grounded facilitation flow that clarifies the problem first, then explores options, then converges to a decision-ready direction.

## Preflight
Run `use-memory`, then start brainstorming.

Start with the real decision to make, not solution details.
Carry only constraints/preferences relevant to this decision.
When the user asks for papers or latest evidence, verify with primary sources and include publication dates.

## Canonical Definition

Brainstorming is structured divergence followed by structured convergence.
- Divergence creates multiple candidate ideas before judgment.
- Convergence evaluates ideas against explicit criteria and picks a next action.
- Success means the selected direction is testable, scoped, and tied to the problem statement.

## Evidence Base (Human vs LLM, as of 2026-02)

- Human verbal group brainstorming can underperform nominal individual ideation due to production blocking and evaluation pressure (Diehl and Stroebe, 1987).
- Parallel and electronic brainstorming can mitigate blocking and improve output quantity and quality (Dennis and Valacich, 1993).
- LLM-assisted ideation often improves individual idea fluency and average quality (Lee and Chung, Nature Human Behaviour, 2024; Doshi and Hauser, Science Advances, 2024).
- Group-level evidence shows LLM-assisted pools can lose idea diversity through anchoring and homogenization if unstructured (Meincke et al., Nature Human Behaviour, 2025; Doshi and Hauser, 2024).
- 2026 evidence shows frontier models can beat average human divergent scores, while top human creators can still lead on strongest outputs (Bellemare-Pepin et al., Scientific Reports, January 21, 2026).

## Best Default (Picked)

Use Human-led Hybrid Brainstorming by default.
- Step A: Human framing plus independent first-pass ideas.
- Step B: LLM expansion for breadth, combinations, and edge cases.
- Step C: Human convergence for value judgment, risk checks, and final selection.

Why this default is best:
- Preserves human originality and context ownership.
- Gains LLM speed and coverage.
- Reduces anchoring risk from LLM-only ideation.
- Avoids verbal group blocking risks from human-only discussion.

## Modes

- Ideation mode: Explore and narrow options without forcing a full design.
- Design mode: Produce an implementation-ready spec only when requested or after a direction is chosen.
- Research mode: When the user asks for definitions, papers, or latest evidence, summarize primary findings before recommending a direction.

## Rules

1. Ask exactly one question per turn.
2. Ask only questions that change a decision.
3. Ask questions as a short pickup list, not open-ended prose.
4. Use 2-4 numbered options plus `Other`.
5. Keep option labels short (2-6 words) with one-line impact.
6. Start with a one-sentence problem statement before proposing solutions.
7. Ground in current context first (files, docs, state) before deep ideation.
8. Keep answers concrete, concise, and testable.
9. Do divergence before convergence.
10. Explicitly compare `Human-only`, `LLM-only`, and `Human-led hybrid` when brainstorming method is in scope.
11. Add one de-anchoring move before convergence (for example, force one opposite-direction option).
12. Rank options with explicit criteria (impact, feasibility, risk, speed by default).
13. Do not force design output unless requested.
14. Keep outputs short and decision-focused.

## Workflow

1. Ground context and frame the problem:
- Summarize relevant current state.
- Confirm goal, constraints, non-goals, and success criteria.
- Write a one-sentence problem statement.
- Include only the memory constraints/preferences that affect the current decision.
- If a question is needed, use:
  `Q1: <decision-shaping question>`
  `Pick one:`
  `1. <Option A> - <impact>`
  `2. <Option B> - <impact>`
  `3. <Option C> - <impact>`
  `4. Other - <one-line answer>`

2. Diverge:
- Propose 2-4 distinct approaches.
- If method choice is relevant, include `Human-only`, `LLM-only`, and `Human-led hybrid` variants.
- Include tradeoffs for each (value, effort, risk).
- Lead with a recommended option and why.

3. Stress-test:
- List top failure modes and assumptions for the leading option.
- Run one de-anchoring prompt and capture at least one contrarian alternative.

4. Converge:
- Rank options against explicit criteria.
- Select one direction, confidence level, and smallest next validating step.
- If decision is unclear, identify the minimum info needed to decide.

5. Optional design handoff (Design mode only):
- Define architecture/components/interfaces.
- Define data flow, error handling, and edge cases.
- Define testing and acceptance criteria.

## Output Templates

### Ideation Summary

- Problem:
- Goal:
- Constraints:
- Decision criteria:
- Options considered:
- Recommended option:
- Why:
- Open risks:
- Next decision:

### Research Snapshot (Optional)

- Question:
- Sources (primary, dated):
- Human-only findings:
- LLM-only findings:
- Hybrid synthesis:
- Decision impact:

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

- Skipping problem definition and jumping to solutions.
- Jumping into implementation before option comparison.
- Presenting generic options with no tradeoffs or criteria.
- Treating first LLM output as final.
- Running LLM-only ideation without de-anchoring.
- Over-scoping beyond stated goals.
- Forcing a full spec when the user asked to brainstorm only.
