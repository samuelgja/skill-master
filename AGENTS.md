# Repository Guidelines

## Project Structure & Module Organization
- `skills/` contains the core content. Each skill lives in `skills/<skill-name>/SKILL.md` (for example, `skills/fix/SKILL.md`).
- `installation/` stores agent-specific install/update instructions: `claude.md`, `codex.md`, and `opencode.md`.
- `assets/` holds shared static assets (currently `assets/logo.svg`).
- `README.md` is the public entry point and should stay aligned with any structural or workflow changes.
- `docs/tasks/` and `docs/done-tasks/` are referenced by workflow docs; create them when task tracking is needed.

## Build, Test, and Development Commands
This repository is documentation-first and does not define a compiled build.
- `rg --files` lists tracked files quickly.
- `find skills -maxdepth 2 -type f | sort` verifies skill files and paths.
- `cat skills/<name>/SKILL.md` reviews a skill before editing.
- `git log --oneline -n 15` checks recent commit style before committing.

## Coding Style & Naming Conventions
- Write concise Markdown with short sections, actionable bullets, and minimal filler.
- Keep skill directories in kebab-case (for example, `execute-task`) and keep the canonical file name as uppercase `SKILL.md`.
- Prefer relative repository paths in docs (for example, `skills/task/SKILL.md`).
- Use ASCII unless a file already requires other characters.

## Testing Guidelines
- There is no automated test framework configured yet.
- Treat verification as content integrity checks: correct paths, valid Markdown headings, and consistency between `README.md`, `installation/*.md`, and modified skill docs.
- For workflow edits, validate examples end-to-end by reading the referenced files and confirming they exist.

## Commit & Pull Request Guidelines
- Follow the existing commit pattern: `feat: ...`, `fix: ...`, `init`.
- Keep commits focused to one logical change (for example, one skill update plus related README adjustments).
- PRs should include: purpose, files changed, user-visible impact, and any follow-up tasks.
- Link related issues when available and include before/after snippets for substantial documentation rewrites.

## Security & Configuration Tips
- Do not commit secrets, tokens, or machine-specific credentials.
- Keep installation URLs on HTTPS and point to maintained repository paths.
