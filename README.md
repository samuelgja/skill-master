<p align="center">
  <img src="assets/logo.svg" alt="skill-master logo" width="720" />
</p>

# skill-master

Practical, strict skills for coding agents.

Use this repo when you want agents to:
- think before coding
- plan work clearly
- execute plans without drifting
- fix bugs with evidence, not guesses

## Why this repo

Most skill packs are too verbose or too generic.  
`skill-master` focuses on short, executable workflows that work in real projects.

## Quick Start

Pick your agent and paste one command:

### Claude Code

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/claude.md
```

### Codex

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/codex.md
```

### OpenCode

```text
Fetch and follow instructions from https://raw.githubusercontent.com/samuelgja/skill-master/main/installation/opencode.md
```

After install, start a new session and ask:
- "help me plan this feature"
- "execute this plan"
- "let's debug this failing test"

The matching skill should auto-trigger.

## Included Skills

| Skill | Purpose |
|---|---|
| `brainstorm` | Explore ideas, converge decisions, optional design handoff |
| `task` | Create short PM-style phased plans with blocking/non-blocking tasks |
| `execute-task` | Execute approved plans in single-agent or parallel mode |
| `fix` | Test-first bug fixing with clear evidence and strict stop rules |

## Recommended Workflow

1. `brainstorm` -> shape direction
2. `task` -> create execution plan in `docs/plans/`
3. `execute-task` -> implement and verify
4. `fix` -> handle regressions with test-first flow
5. Completed plans move to `docs/done-tasks/`

## Repository Structure

- `skills/` - skill folders (`<name>/SKILL.md`)
- `installation/` - install guides by platform
- `docs/plans/` - active plans
- `docs/done-tasks/` - completed plans
