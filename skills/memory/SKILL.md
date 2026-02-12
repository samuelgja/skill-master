---
name: memory
description: Use to capture and apply stable user decisions/preferences with fast writes. Default scope is local project memory; use global only when explicitly requested.
---

# Memory

Capture reusable user decisions so the agent stops repeating avoidable mistakes.

## Quick Path

Before this workflow, run `use-memory` to load existing constraints/preferences, then continue with memory updates.

## Goal

Store short, durable memory entries in a format that is:
- fast to append
- easy to dedupe
- easy to retrieve into prompts

## Scope Rules

1. Default scope is `local`.
2. Use `global` only when the user explicitly says `global` (or equivalent).
3. Never ask a scope question if the user did not specify one; use `local`.

Paths:
- Local: `docs/memory/memories.ndjson`
- Global: `~/.skill_master/memory/memories.ndjson`

## 2026 Practical Format (Recommended)

Use an append-only NDJSON memory log with atomic triple-style facts plus metadata.

Each memory entry should include:
- `id`: unique id
- `ts`: ISO timestamp
- `scope`: `local | global`
- `kind`: `rule | preference | constraint`
- `triple`: `{ "subject": "...", "predicate": "...", "object": "..." }`
- `canonical`: short normalized sentence
- `strength`: integer, starts at `1`, increments on repeats
- `source`: `user_direct`
- `tags`: short list
- `dedupe_key`: normalized key for upsert behavior

Why this format:
- Triple fields keep memories machine-mergeable.
- `canonical` keeps them human-readable.
- NDJSON supports very fast append and simple tooling.
- `strength` + `ts` supports relevance ranking without heavy infra.

## Fast Add Flow

Trigger examples:
- "add to memory that never run build repeatedly because it is heavy"
- "global: add to memory that I prefer pnpm"

Write flow:
1. Detect scope (`global` only if explicit; else `local`).
2. Normalize into one atomic rule.
3. Build triple + canonical sentence.
4. Compute `dedupe_key` (lowercase normalized `subject|predicate|object`).
5. If key exists, update `ts` and increment `strength` instead of adding duplicate.
6. If key does not exist, append a new NDJSON line.
7. Return a one-line confirmation with scope and canonical memory.

## Retrieval Flow (Prompt Injection)

Before planning/execution:
1. Load memory entries from selected scope.
2. Rank by:
- explicit tag match
- `kind` priority (`constraint` > `rule` > `preference`)
- `strength`
- recency
3. Inject only top 3-7 memories to avoid prompt bloat.
4. Prefer high-strength repeated rules over one-off notes.

## Conflict Rules

1. Same `dedupe_key`: treat as reinforcement (`strength + 1`).
2. Opposing rules on same subject/predicate: newest wins unless older rule is marked `constraint`.
3. If conflict is ambiguous, keep both and mark with conflict tag for later cleanup.

## Safety

- Do not store secrets, tokens, passwords, or private keys.
- Keep entries short and behavioral.
- Avoid storing transient context that will expire immediately.
