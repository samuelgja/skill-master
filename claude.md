# Repository Guidelines

This repo ships two skills for coding agents: `create-research` and `research`. Inspired by karpathy/autoresearch, generalized to any language and monorepo.

## Project Structure

- `skills/create-research/SKILL.md` — scaffolder
- `skills/research/SKILL.md` — autonomous executor
- `installation/{claude,codex,opencode}.md` — install + update guides
- `assets/` — static assets
- `README.md` — public entry point

User repos that adopt these skills get a `research/<topic>/` directory containing the rules, validator, leaderboard, run log, and (for parallel mode) worktrees. That directory is created by `create-research`, not by this repo.

## Authoring Conventions

- Skill folders use kebab-case. Canonical filename is uppercase `SKILL.md`.
- Each `SKILL.md` opens with frontmatter: `name`, `description`. Description must include trigger phrases.
- Markdown is concise. Bullets over prose. Short sections. ASCII unless the file already needs more.
- When referring to other files, use repo-relative paths.

## Verification

Documentation-first repo. No build.

- `find skills -maxdepth 2 -type f | sort` — confirms skill files exist
- `cat skills/<name>/SKILL.md` — review before edit
- `git log --oneline -n 15` — match commit style

Content integrity = correct paths, valid headings, alignment between `README.md`, `installation/*.md`, and `SKILL.md` files.

## Skill Design Rules

- `create-research`: ask one decision at a time. Pick-list. Never write code-level mutation instructions. Output = spec + validator only.
- `research`: never asks the user mid-loop. Never stops until stop condition or explicit `stop`/`halt`/`abort`. Uses `ScheduleWakeup` for long iterations. Uses `TaskCreate`/`TaskUpdate` for visibility.
- Both: validator contract is fixed — final stdout line `RESULT_JSON: {"score": <num>, "valid": <bool>, "notes": "<string>"}`.

## Commits & PRs

- Pattern: `feat: ...`, `fix: ...`, `docs: ...`, `init`.
- One logical change per commit (one skill update plus related README adjustments is fine).
- PRs include: purpose, files changed, user-visible impact, follow-up tasks.

## Security

- No secrets, tokens, machine-specific credentials.
- Installation URLs stay HTTPS, point to maintained paths.
