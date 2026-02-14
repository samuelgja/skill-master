---
name: learn
description: Use to store stable user rules in plain language. Write one clear memory sentence per line in Markdown. Default scope is local; use global only when explicitly requested.
---

# Learn

Capture reusable user rules so the agent remembers them without JSON.

## Goal

Store memory as clear, self-explanatory lines in Markdown.

## Scope Rules

1. Default scope is `local`.
2. Use `global` only when the user explicitly says `global` (or equivalent).
3. Never ask a scope question if the user did not specify one; use `local`.

Paths:
- Local: `docs/memory/memory.md`
- Global: `~/.skill_master/memory/memory.md`

## Memory Line Format (Required)

- One memory per line.
- One sentence only.
- Keep this shape: `<Subject> <rule/preference> because <reason>.`
- No JSON.
- No tables.
- No summaries.

Good examples:
- `Keep CI runs minimal because full builds are heavy in this repository.`
- `Prefer bun commands because this project scripts are tuned for bun.`
- `Ignore git commands`

Bad examples:
- `{"k":"rule","t":["ci","minimize_runs","heavy"]}`
- `Need speed.`

## Fast Add Flow

Trigger examples:
- "add to memory that keep CI runs minimal because builds are heavy"
- "global: add to memory that prefer bun because this repo uses bun scripts"

Write flow:
1. Detect scope (`global` only if explicit; else `local`).
2. Ensure target path exists; create directories/file if missing.
3. Rewrite into one self-explanatory sentence with a reason.
4. Keep sentence shape: `<Subject> <rule/preference> because <reason>.`
5. If the exact line already exists, do not duplicate.
6. Otherwise append the new line at the end of `memory.md`.
7. Return one-line confirmation with scope and saved sentence.

## Retrieval Notes

Before planning/execution:
1. Load lines from selected `memory.md`.
2. Ignore blank lines and Markdown headings.
3. Prefer latest relevant 3-7 lines.
4. Inject them as direct action rules.

## Safety

- Do not store secrets, tokens, passwords, or private keys.
- Keep entries behavioral and durable.
- Avoid transient details that expire quickly.
