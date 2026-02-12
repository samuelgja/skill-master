---
name: memory
description: Use to capture and apply stable user decisions/preferences with fast writes. Default scope is local project memory; use global only when explicitly requested.
---

# Memory

Capture reusable user decisions so the agent stops repeating avoidable mistakes.

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

## 2026 Practical Format (Compact)

Use append-only NDJSON with compact keys and one atomic memory per line.

Minimal entry shape:
- `k`: `constraint | rule | preference`
- `t`: triple array: `["subject", "predicate", "object"]`
- `w`: repeat weight (starts at `1`, increments on repeats)
- `n`: short normalized sentence

Example:
```json
{"k":"constraint","t":["build","avoid_repeated_runs","heavy"],"w":3,"n":"Never run build repeatedly; it is heavy."}
```

Why this format:
- Triple keeps memory structured and mergeable.
- `n` keeps it readable.
- `w` captures repeated importance without extra metadata.
- No `id`/`ts` overhead; line order provides recency.

## Fast Add Flow

Trigger examples:
- "add to memory that never run build repeatedly because it is heavy"
- "global: add to memory that I prefer pnpm"

Write flow:
1. Detect scope (`global` only if explicit; else `local`).
2. Normalize into one atomic rule.
3. Build compact entry: `k`, `t`, `w`, `n`.
4. Compute key from normalized triple (`t[0]|t[1]|t[2]`).
5. If key exists, increment `w` (do not append duplicate).
6. If key does not exist, append a new NDJSON line with `w=1`.
7. Return one-line confirmation with scope and normalized memory.

## Retrieval Flow (Prompt Injection)

Before planning/execution:
1. Load memory entries from selected scope.
2. Rank by:
- `k` priority (`constraint` > `rule` > `preference`)
- `w`
- recency by line order (newer lines first when tie)
3. Inject only top 3-7 memories to avoid prompt bloat.
4. Prefer high-weight repeated rules over one-off notes.

## Conflict Rules

1. Same triple key: treat as reinforcement (`w + 1`).
2. Opposing rules on same subject/predicate: `constraint` wins, otherwise newest line wins.
3. If conflict is ambiguous, keep both and mark with conflict tag for later cleanup.

## Safety

- Do not store secrets, tokens, passwords, or private keys.
- Keep entries short and behavioral.
- Avoid storing transient context that will expire immediately.
