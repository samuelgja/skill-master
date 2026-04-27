---
name: research
description: Use to execute an autonomous research loop defined by `research-rules.md` and `research-validation.<ext>`. Trigger for "run research", "start the experiment loop", "kick off research", or after `create-research` finishes. Loops forever (never asks user mid-loop) until stop condition fires or user interrupts. Supports sequential (single branch) and parallel (git worktrees) modes. Uses ScheduleWakeup for long iterations and TaskCreate/TaskUpdate for per-iteration visibility. Optionally driven by `/loop` for self-paced re-entry or `/schedule` for cron-style nightly runs. Inspired by karpathy/autoresearch.
---

# research

Execute autonomous research loop. Read `research-rules.md`, run experiments, validate, log, mutate, repeat. **Never stop. Never ask. Until stop condition or human interrupt.**

**Autonomy framing:** the human may be asleep, in a meeting, or away from the computer. They expect to wake up to a leaderboard, not to a question. With a ~5-min iter budget you can do ~12/hour, ~100 over a typical sleep window. Produce results, not check-ins.

## When to trigger

User says "run research", "start research", "execute research", "kick off the experiment loop", or hands off after `create-research`. Path to `research-rules.md` is the input.

## Non-Negotiables

1. **NEVER ASK THE USER ANYTHING DURING THE LOOP.** Setup phase may ask 1 confirmation. After loop starts, no questions until stop condition or interrupt. Period.
2. Every iteration must `git commit` before validation so reset is clean.
3. Validator's `RESULT_JSON: {...}` line is the single source of truth for score.
4. Forbidden paths in rules.md are inviolable. Touch them = abort run.
5. Required invariants must run after every keep-decision; broken invariant = revert + log `crash`.
6. Every iteration writes one row to `research/<topic>/results.tsv`.
7. Use `TaskCreate` for the run; `TaskUpdate` per iteration. Tracks progress visibly.
8. Use `ScheduleWakeup` when an iteration runs longer than the cache window. Wake up to check, validate, decide.
9. Sequential mode = one branch. Parallel mode = N worktrees, evolutionary selection per round.
10. Direction-aware comparison: `minimize` → lower is better; `maximize` → higher is better.
11. Crashes never block the loop. Log `crash` status, revert, continue.
12. If user types ANYTHING that is not "stop" / "halt" / "abort", treat as side-channel context, log to `notes.md`, keep looping.

## Inputs

- Path to `research-rules.md` (required). Adjacent `research-validation.<ext>` discovered automatically.
- If user gives only a topic, look in `research/<topic>/research-rules.md`.

## Phase 1 — Setup (one-shot, no questions)

1. Read `research-rules.md`. Parse all sections.
2. Verify and auto-recover (do NOT ask):
   - Repo is a git work tree. If not, abort with explicit reason.
   - Working tree dirty? Auto-stash to `research/<topic>/pre-run.stash` (`git stash push -u -m "research-<topic>-prerun" && git stash show -p stash@{0} > research/<topic>/pre-run.stash && git stash drop`). Log to `notes.md`. Continue silently.
   - Editable files exist. Forbidden paths and invariants resolve.
   - Validator wrapper `research/<topic>/validate.sh` exists and is executable; if missing, generate from `research-validation.<ext>` extension.
3. Run validator once on current code as **baseline** (`bash research/<topic>/validate.sh > research/<topic>/run.log 2>&1` first if needed). Record commit + score + cost in `results.tsv` with status `keep`, description `baseline`.
4. Create branch per mode:
   - sequential: `git checkout -b research/<topic>/<run-tag>`
   - parallel: prepare `research/<topic>/worktrees/` and create N worktrees on branches `research/<topic>/<run-tag>-w<N>-r0`.
5. `TaskCreate` a parent task: `research <topic> [<mode>] budget=<N> iters / <T> wall / <K> no-improve`.
6. Print one-line confirmation. Do not ask anything else. Begin Phase 2 immediately.

## Phase 2 — Loop (autonomous)

### Sequential loop

```
LOOP FOREVER:
  iter += 1
  TaskUpdate parent: in_progress, "iter <iter>"
  pick mutation idea (LLM free-form, optionally constrained by `Mutation.knobs`)
  edit only files listed in `Target.editable`
  verify no forbidden path touched (git diff --name-only ∩ forbidden = ∅, exception: results.tsv/run.log/notes.md; else abort iter)
  git add -A && git commit -m "exp <iter>: <one-line idea>"
  run command (redirect to research/<topic>/run.log; do NOT tee)
  if iteration exceeds rules `Time budget` * 2:
    kill, mark crash, revert
    continue
  invoke validator: `bash research/<topic>/validate.sh`
  parse `RESULT_JSON:` line
  if not valid:
    log crash, git reset --hard HEAD~1
    no_improve_streak += 1
  else:
    apply Simplicity criterion:
      better score (direction-aware) AND not uglier -> keep
      equal score AND simpler/smaller diff -> keep (deletion is a win)
      tiny gain + ugly hack -> discard
      worse score -> discard
    if keep:
      log keep, advance branch (commit stays)
      best = score
      no_improve_streak = 0
    else:
      log discard, git reset --hard HEAD~1
      no_improve_streak += 1
  run required invariants; if any fail: revert last keep, log crash, no_improve_streak += 1
  check stop conditions; if hit: break
  if expected next iter wall-time > 270s: ScheduleWakeup with delaySeconds = min(iter_time + 60, 1800), prompt = "/loop research <topic>" (or autonomous-loop sentinel)
```

### Parallel loop

```
round = 0
seed = current branch HEAD
LOOP FOREVER:
  round += 1
  for w in 1..N parallel:
    worktree branch: research/<topic>/<run-tag>-w<w>-r<round>
    spawn subagent in worktree (Agent tool, isolation: worktree)
    subagent task: "mutate target file(s) per rules.md, commit, run, validate, write results to results.tsv with worktree prefix"
  wait for all subagents (use ScheduleWakeup if expected > 270s)
  collect scores from results.tsv rows of this round
  apply Selection rule:
    top-1: pick single best, advance main branch to that commit, drop other worktrees
    top-K: keep K worktrees as parents for next round
    tournament: pairwise compare, winners advance
  prune losing worktrees: `git worktree remove <path>`
  no_improve_streak += (1 if round_best <= prev_best else 0)
  run required invariants on survivor(s); if broken: revert survivor, log crash
  check stop conditions; if hit: break
```

### Stop conditions (any one fires → graceful stop)

- `iter >= Budget.max_iterations`
- wall-clock since setup `>= Budget.wall_clock_cap`
- `no_improve_streak >= Budget.no_improve_streak_cap`
- forbidden path touched (abort, do not continue)
- user message containing word `stop` / `halt` / `abort` (case-insensitive)

## Periodic wakeups (long runs)

When an experiment or round will outlast the prompt cache window (~5 min), use `ScheduleWakeup`:

- `delaySeconds`: pick by expected work duration. Under cache: `60-270`. Beyond: `1200-1800`. Avoid `300`.
- `prompt`: pass the same invocation back so the loop resumes (`research <topic>` or autonomous sentinel).
- `reason`: short specific string, e.g. `"checking iter 7 of <topic>, ~6min run"`.

Do not poll with sleep. Do not block. Schedule and yield.

### Optional `/loop` mode

If the user kicked off via `/loop research <topic>`, the loop skill drives re-entry. Stay self-paced via `ScheduleWakeup` exactly the same way — just pass back the same `/loop` prompt verbatim. This pattern is ideal when iterations are heterogeneous in length.

### Optional `/schedule` (cron) mode

Long-horizon research can be cron-armed: user runs `/schedule` once to fire `research <topic>` nightly, weekly, etc. Each cron fire enters Phase 1, sees existing rules+results, and resumes the loop. Cron NEVER asks the user; it just resumes.

## Logging

Append one row per iteration to `research/<topic>/results.tsv`:

```
commit\tscore\tcost\tstatus\tdescription
```

5 columns, matching karpathy. `cost` is the secondary metric configured in rules.md (`memory_gb` / `latency_ms` / `dollars` / `n/a`). Crashes record `0.000000` for score and `0.0` for cost.

Examples:

```
commit	score	cost	status	description
a1b2c3d	0.997900	44.0	keep	baseline
b2c3d4e	0.993200	44.2	keep	bumped lr to 0.04
c3d4e5f	1.005000	44.0	discard	swap activation -> gelu
d4e5f6g	0.000000	0.0	crash	OOM after width x2
```

`run.log` is overwritten per iteration. Do not commit. Already gitignored by `create-research`.

If user message arrives mid-loop with non-stop content, append to `research/<topic>/notes.md` with timestamp + iteration index. Continue loop.

## Mutation guidance

- Default: LLM free-form. Read `Mutation.notes` for hypothesis hints. Vary depth: small tweaks first (hyperparams), then medium (algorithmic swap), then large (architectural change). After 3 no-improves, force a category jump.
- If `Mutation.knobs` listed: still free-form by default, but treat knobs as the most-likely productive dials.
- If style is `knobs-only`: enumerate combos via simple search (random/Bayesian-lite/grid bounded by budget).

### Think harder (out-of-ideas protocol)

When the no-improve streak is high or you feel stuck, do NOT ask the user. Run this menu, in order:

1. Re-read every in-scope file (target, validator, rules.md notes section). New angle often hides in plain sight.
2. Re-read external references named in `Mutation.notes` (papers, docs, related repos).
3. Combine previous near-misses: pick two `discard` rows with promising directions and merge their ideas.
4. Try a radical change: swap algorithm family entirely, change data layout, change the objective term order.
5. Try a deletion: remove a component and see if score holds. Deletions that hold = wins.
6. Reset assumptions: re-read forbidden paths to confirm what's actually fixed vs free.

Only after exhausting this menu and still flat, the no-improve streak cap fires naturally. Stop is mechanical, not asked.

## Crash handling (use judgment, do not ask)

Karpathy rule: **trivial fix → patch and retry once; fundamental → log crash and skip.**

- Trivial (typo, missing import, off-by-one in your own diff): fix in-place, re-run once. If second run also crashes, log `crash`, revert, continue.
- Fundamental (idea is broken — wrong dimensions, dependency missing, OOM at any size): log `crash`, `git reset --hard HEAD~1`, continue. Do not retry.
- Validator script crash (no `RESULT_JSON:` line): treat as crash. After 3 consecutive validator crashes, the validator itself is broken — abort run with explicit message and exit. Do NOT prompt the user; just stop.
- Invariants broken: revert latest keep, log `crash`, treat as no-improve. Never bypass invariants "to chase a breakthrough".
- Run exceeds `Time budget * 2`: kill the process, mark `crash`, revert, continue.

## Stop & report

On graceful stop:

- Print summary: iterations, keeps, best score, branch with best commit.
- Print top-5 keeps from results.tsv.
- Print worktree cleanup instructions if parallel mode used.
- `TaskUpdate` parent → completed.
- Do not auto-merge. Tell user: "Best run on branch `<branch>` at commit `<sha>`. Merge or cherry-pick when ready."

On forbidden-path abort:

- `git reset --hard <last-keep>`, print abort reason, mark task failed, exit.

## Behavior Guardrails

- Human-asleep default: assume the human is unavailable. Generate experiments, not check-ins. ~12 iters/hour for a 5-min budget; ~100 over a sleep window.
- Do NOT ask user anything during loop. Not "should I keep going?". Not "is this enough?". Never.
- Do NOT modify `research-rules.md` or `research-validation.<ext>` mid-run.
- Do NOT touch forbidden paths. Diff-check every commit.
- Do NOT leave worktrees lying around after parallel run completes.
- Do NOT use `tee` for run output; flooding context kills the loop.
- Do NOT commit `run.log` or `results.tsv`.
- Do NOT compress or summarize results.tsv; append-only.
- Do NOT amend commits; each experiment = new commit.
- Do NOT bypass invariants when "close to a breakthrough"; rules are rules.

## Handoff

Loop done → user gets a leaderboard, a winning branch, and a clean tree. User decides merge.
