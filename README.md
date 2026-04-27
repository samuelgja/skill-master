<p align="center">
  <img src="assets/logo.svg" alt="skill-master logo" width="720" />
</p>

# skill-master

Two skills. Both for autonomous research. Inspired by [karpathy/autoresearch](https://github.com/karpathy/autoresearch), but language-agnostic and monorepo-friendly.

## Idea

Hand the agent a problem. Let it experiment forever. Wake up to a leaderboard.

- **`create-research`** — scaffolds a strict spec (`research-rules.md`) + runnable validator (`research-validation.<ext>`).
- **`research`** — executes the loop. Mutates target file, commits, runs, validates, keeps or reverts. Never asks. Never stops until budget hit or you say so.

Two modes:
1. **Sequential** — single branch, one experiment at a time, `git reset` on regress (karpathy-style).
2. **Parallel** — N git worktrees per round, evolutionary survivor selection across rounds.

Mutation: LLM free-form by default. Optional named knobs in rules.md.

## Install

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

Same URL as install. Each guide includes update steps.

After install or update, restart your session.

## Skills

| Skill | Purpose |
|---|---|
| `create-research` | Scaffold `research-rules.md` + `research-validation.<ext>` for a new experiment loop |
| `research` | Execute the loop autonomously. Sequential or parallel. Forever until stop. |

## Workflow

1. Run `create-research`. Answer the pick-list questions (goal, target file, run cmd, validator, mode, budget, forbidden paths, invariants).
2. Skill writes `research/<topic>/research-rules.md` and `research-validation.<ext>`.
3. Run `research <topic>`. Confirm setup once. Loop starts.
4. Walk away. Loop runs forever or until a stop condition fires.
5. Wake up. Read `research/<topic>/results.tsv` leaderboard. Check the winning branch. Merge or discard.

## Repository layout

- `skills/create-research/SKILL.md`
- `skills/research/SKILL.md`
- `installation/{claude,codex,opencode}.md`
- `assets/logo.svg`

User repos using these skills get:

- `research/<topic>/research-rules.md` — strict spec
- `research/<topic>/research-validation.<ext>` — runnable validator
- `research/<topic>/results.tsv` — append-only leaderboard (gitignored)
- `research/<topic>/run.log` — last run output (gitignored)
- `research/<topic>/notes.md` — side-channel context dropped by the user during loop
- `research/<topic>/worktrees/` — parallel mode worktrees (gitignored)

## Contract for `research-validation.<ext>`

- Exits 0 on completion (success OR controlled failure).
- Final stdout line, verbatim:
  ```
  RESULT_JSON: {"score": <number>, "valid": <bool>, "notes": "<string>"}
  ```
- Score direction (`minimize` / `maximize`) lives in `research-rules.md`.

Templates included in `create-research` for `.py`, `.ts`, `.sh`. Other languages: same contract, your skeleton.

## Never-stop clause

`research-rules.md` carries a "Never-stop" section. The executor never asks the user mid-loop. Side messages get logged to `notes.md`. Loop halts only on stop conditions or explicit `stop`/`halt`/`abort`.
