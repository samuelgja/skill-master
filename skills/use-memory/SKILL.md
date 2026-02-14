---
name: use-memory
description: Use at the start of work to quickly load relevant memory constraints and preferences before other skills execute.
---

# Use Memory

Load high-signal memory context first, then continue with the requested task.

## Goal

Inject the smallest useful memory set before planning, coding, or debugging.

## Fast Flow

1. Default to local memory at `docs/memory/memory.md`.
2. If user explicitly asks for global memory, also read `~/.skill_master/memory/memory.md`.
3. If memory files do not exist, continue without blocking.
4. Parse memory lines:
- use non-empty lines only
- ignore Markdown headings
- treat each line as one self-explanatory rule sentence
5. Rank by relevance to the current task, then recency.
6. Select top 3-7 relevant lines.
7. Convert selected lines to short action rules for the current task.
8. Continue with the requested skill/workflow using those rules.

## Output

Return one short line:
- `Memory loaded: <count> entries (<scope>).`

## Guardrails

- Keep memory injection short; avoid prompt bloat.
- Never block execution if memory files are missing.
- Never store new memory in this skill; use `learn` skill for writes.
