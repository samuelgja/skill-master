<p align="center">
  <img src="assets/logo.svg" alt="skill-master logo" width="720" />
</p>

# skill-master

Practical, strict skills for coding agents.

Use this repo when you want agents to:
- think before coding
- plan work clearly
- execute tasks without drifting
- fix bugs with evidence, not guesses

## Why this repo

Most skill packs are too verbose or too generic.  
`skill-master` focuses on short, executable workflows that work in real projects.

## Install

Pick your agent and paste one command:

### Claude Code

```text
Fetch and follow installation instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/claude.md
```

### Codex

```text
Fetch and follow installation instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/codex.md
```

### OpenCode

```text
Fetch and follow installation instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/opencode.md
```

## Update

Pick your agent and paste one command:

### Claude Code

```text
Fetch and follow update instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/claude.md
```

### Codex

```text
Fetch and follow update instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/codex.md
```

### OpenCode

```text
Fetch and follow update instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/opencode.md
```

After install or update, start a new session and begin your task.

## Included Skills

| Skill | Purpose |
|---|---|
| `use-memory` | Quickly load relevant memory constraints/preferences before other workflows |
| `brainstorm` | Explore ideas, converge decisions, optional design handoff |
| `learn` | Capture and reuse user decisions/preferences as one-line memory rules |
| `task` | Create short PM-style phased tasks with blocking/non-blocking tasks |
| `execute-task` | Execute approved tasks in single-agent or parallel mode |
| `fix` | Test-first bug fixing with clear evidence and strict stop rules |

## Recommended Workflow

1. `use-memory` -> quickly load relevant memory before other workflows
2. `brainstorm` -> shape direction
3. `learn` -> store durable preferences/constraints
4. `task` -> create execution plan in `docs/tasks/`
5. `execute-task` -> implement and verify
6. `fix` -> handle regressions with test-first flow
7. Completed tasks move to `docs/done-tasks/`

## Repository Structure

- `skills/` - skill folders (`<name>/SKILL.md`)
- `installation/` - install guides by platform
- `docs/memory/` - local project memory log (`memory.md`)
- `docs/tasks/` - active tasks
- `docs/done-tasks/` - completed tasks
