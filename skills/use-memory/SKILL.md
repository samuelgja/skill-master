---
name: use-memory
description: Use at the start of work to quickly load relevant memory constraints and preferences before other skills execute.
---

# Use Memory

Load high-signal memory context first, then continue with the requested task.

## Goal

Inject the smallest useful memory set before planning, coding, or debugging.

## Fast Flow

1. Default to local memory at `docs/memory/memories.ndjson`.
2. If user explicitly asks for global memory, also read `~/.skill_master/memory/memories.ndjson`.
3. If memory files do not exist, continue without blocking.
4. Rank memories by:
- kind priority (`constraint` > `rule` > `preference`)
- strength
- recency
5. Select top 3-7 relevant entries.
6. Convert to short action rules for the current task.
7. Continue with the requested skill/workflow using those rules.

## Output

Return one short line:
- `Memory loaded: <count> entries (<scope>).`

## Guardrails

- Keep memory injection short; avoid prompt bloat.
- Never block execution if memory files are missing.
- Never store new memory in this skill; use `memory` skill for writes.
