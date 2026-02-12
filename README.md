# skill-master

A curated skills repository for coding agents.

This repo contains strict, practical skills for:
- brainstorming ideas before coding
- creating concise execution plans
- executing plans with single or parallel agents
- fixing bugs with test-first evidence

## Skills in this repo

- `brainstorm`: ideation + optional design handoff
- `task`: brief PM-style phased plans
- `execute-task`: run approved plans safely and track status
- `fix`: strict test-first bug fixing

## How skills are used

After installation, start a new session and ask for something that matches a skill trigger, for example:
- "help me plan this feature"
- "execute this plan"
- "let's debug this failing test"

The agent should automatically invoke the relevant skill.

## Installation

### Claude Code

Tell Claude Code:

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/claude.md
```

Detailed docs: `docs/README.claude-code.md`

### Codex

Tell Codex:

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/codex.md
```

### OpenCode

Tell OpenCode:

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/opencode.md
```

## Repo layout

- `skills/`: skill folders (`<name>/SKILL.md`)
- `installation/`: platform install guides
- `docs/plans/`: active task plans
- `docs/done-tasks/`: completed plan archive
