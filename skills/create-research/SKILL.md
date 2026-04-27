---
name: create-research
description: Use to scaffold an autonomous research run. Trigger for "create me research", "set up research", "research better algorithm for X", or any request to bootstrap an experiment loop. Proposes sensible defaults; asks only for the essentials it cannot infer (goal, target file, run cmd, validator, metric direction). Writes `research/<topic>/research-rules.md` and `research/<topic>/research-validation.<ext>` that the `research` skill executes. Inspired by karpathy/autoresearch but language-agnostic.
---

# create-research

Scaffold strict research run. Two artifacts: `research-rules.md` (spec) + `research-validation.<ext>` (runnable validator). Output drives the `research` executor skill.

## When to trigger

User says "create research", "set up research", "research better algorithm for X", "kick off experiment loop", "auto-tune Y". Works in any repo, any language.

## Non-Negotiables

1. Never write code-level mutation instructions. Only spec + validator.
2. Propose-and-confirm, do not interrogate. One batched prompt with proposed defaults; user fills only blanks. After Phase 1 confirmation, no further questions ever — the `research` skill enforces silent autonomy.
3. Infer aggressively. Detect language from target file extension. Propose run tag from today's date + topic slug. Propose forbidden paths from common conventions (lockfiles, CI, secrets, `prepare.py`-style fixed harnesses). Only block on truly missing essentials: target file(s), run command, metric direction.
4. Validator must be self-contained, deterministic, runnable with one command, exit 0 = valid, print final line `RESULT_JSON: {...}` containing `score`, `valid`, optional `notes`.
5. `research-rules.md` must follow exact schema below. Do not rename labels.
6. Default mode = sequential. Parallel only if `git worktree` available and user opts in.
7. Default location = `research/<topic>/` at repo root. Override only if user requests.
8. Branch convention: `research/<topic>/<run-tag>` (sequential), `research/<topic>/<run-tag>-w<N>-r<round>` (parallel worktrees, round-stamped).
9. Add `results.tsv`, `run.log`, `worktrees/`, `pre-run.stash` to repo `.gitignore` if not already.
10. `research-rules.md` must list explicit forbidden paths AND required invariants. Carve a logging exception: `results.tsv`, `run.log`, `notes.md` inside `research/<topic>/` are write-allowed even though the dir is forbidden for code edits.
11. Output research dir contains only: `research-rules.md`, `research-validation.<ext>`, `validate.sh` (one-line wrapper), `README.md` (1-paragraph hint), and `.gitignore` for `run.log`/`results.tsv`/`worktrees/`/`pre-run.stash`.
12. Time budget per run is REQUIRED, not optional. Fixed budget makes runs directly comparable (karpathy invariant).

## Workflow

### 1. Context lock

- Restate user goal in one line.
- Identify target file(s) the agent will mutate. If unknown, ask.
- Identify validator language. Default = same as target. If target is multi-language or none, ask.
- Detect git availability: `git rev-parse --is-inside-work-tree`. If not git, parallel mode disabled.
- Detect `git worktree` availability for parallel mode.

### 2. One-shot clarification (single batch, defaults pre-filled)

Propose every field in one message. User confirms or overrides. Only the four ESSENTIAL fields block: target file(s), run command, time budget, metric direction. Everything else has a default — if the user does not override, take the default and proceed.

Print proposal in this exact form, all fields together:

```
Proposed research setup:

ESSENTIALS (must confirm or override):
- Target file(s): <inferred or "?">
- Run command: <inferred or "?">
- Time budget per run: <inferred or "?"> (fixed, makes runs comparable)
- Metric direction: minimize | maximize  (default: minimize)

DEFAULTS (override with one line each, or accept all):
- Topic slug: <kebab from goal>
- Run tag: <YYYYMMDD-slug>
- Mode: sequential   (alt: parallel — only if `git worktree` available)
- Parallel candidates per round: 4    (only used in parallel mode)
- Selection: top-1                    (alt: top-K, tournament)
- Validator language: <from target ext>
- Validator parser: reads run.log, prints RESULT_JSON
- Forbidden paths: lockfiles, CI config, secrets, fixed harness, research/<topic>/ (logs excepted)
- Required invariants: <none unless user lists>
- Mutation style: llm-free-form        (alt: free-form-with-knobs, knobs-only)
- Stop conditions: 50 iters OR 8h wall-clock OR 10 no-improve streak
- Logging columns: commit, score, cost, status, description (cost = memory_gb | latency_ms | $ | n/a)
- Wakeup convention: ScheduleWakeup if iter >270s; TaskCreate parent + TaskUpdate per iter

Reply: "go" to accept all, OR list overrides one per line.
```

If essentials still blank after first reply, re-ask only those, in one batch.

### 3. Detect/derive defaults

| Input | Default |
|---|---|
| topic slug | kebab-case from goal, max 5 words |
| run tag | today `YYYYMMDD` + short topic |
| validator ext | from target language: py→`.py`, ts→`.ts`, js→`.mjs`, go→`.go`, rs→`.rs`, sh→`.sh`, other→`.sh` |
| parallel N | 4 |
| selection | top-1 |
| stop | 50 iters OR 8h wall-clock OR 10 no-improve streak |

### 4. Write artifacts

Create dir `research/<topic>/`:

- `research-rules.md` — exact schema below
- `research-validation.<ext>` — language-appropriate skeleton (see below)
- `README.md` — 3 lines: topic, mode, "run with `research` skill"
- `.gitignore` — `run.log`, `results.tsv`, `worktrees/`, `*.swp`

Append to repo root `.gitignore` if missing: `research/*/run.log`, `research/*/results.tsv`, `research/*/worktrees/`.

### 5. Confirm

Print summary: topic, location, mode, target, run cmd, validation cmd, metric, budget, forbidden paths, invariants. Tell user how to launch: invoke `research` skill with path to `research-rules.md`.

## `research-rules.md` Schema (Strict)

Use this exact structure. Keep labels verbatim.

```md
# Research: <Topic>

## Header
- Topic: <kebab-slug>
- Goal: <one-line objective>
- Mode: <sequential | parallel>
- Run tag: <YYYYMMDD-slug>
- Branch convention: research/<topic>/<run-tag>[-w<N>]
- Created: <YYYY-MM-DD>
- Inspired by: karpathy/autoresearch

## Target
- Editable file(s): <relative paths, one per line>
- Forbidden paths: <relative paths or globs>
- Required invariants: <commands that must keep exiting 0>

## Run
- Command: <shell command to execute one candidate>
- Time budget: <wall-clock seconds, REQUIRED>  # fixed budget makes runs directly comparable (karpathy invariant)
- Working dir: <repo root | other>
- Env: <vars or "n/a">

## Simplicity criterion
- Equal score + simpler code (fewer lines, less complexity) -> KEEP.
- Tiny score gain + ugly hack -> DISCARD.
- Removing code with equal-or-better score -> always KEEP (deletion is a win).
- Weigh complexity cost against improvement magnitude on every keep decision.

## Validation
- Command: <shell command to validate output>
- Parser: <how metric is extracted; expects final line `RESULT_JSON: {"score": <num>, "valid": <bool>, "notes": "..."}`>
- Metric name: <e.g. val_bpb, accuracy, p99_latency_ms>
- Direction: <minimize | maximize>
- Crash policy: <skip | retry-once | abort>

## Mode
### If sequential
- Branch: research/<topic>/<run-tag>
- Loop: edit -> commit -> run -> validate -> if improved keep, else `git reset --hard HEAD~1`

### If parallel
- Worktree dir: research/<topic>/worktrees/
- Candidates per round: <N>
- Branch per candidate: research/<topic>/<run-tag>-w<N>-r<round>
- Selection: <top-1 | top-K with K=<n> | tournament>
- Mutation source: <best-of-round | top-K parents | seed-only>

## Mutation
- Style: <llm-free-form | llm-with-knobs | knobs-only>
- Knobs (optional, free-form ignores these): <name>: <range/options>; ...
- Notes for the agent: <free-form guidance, e.g. paper references, anti-patterns>

## Budget
- Max iterations: <N>
- Wall-clock cap: <duration>
- No-improve streak cap: <K>

## Logging
- Results file: research/<topic>/results.tsv
- Columns: commit\tscore\tcost\tstatus\tdescription   # 5 cols, matches karpathy
- cost dimension: <memory_gb | latency_ms | dollars | n/a>   # configurable secondary metric so research is resource-aware
- Status values: keep | discard | crash
- Run log: research/<topic>/run.log (overwritten each run, gitignored)
- Leaderboard view: sort results.tsv by score (direction-aware), tiebreak on cost (lower)
- Notes: research/<topic>/notes.md (side-channel context dropped by user during loop, gitignored)

## Autonomy harness
- Task tracking: TaskCreate parent on Phase 1, TaskUpdate per iteration (status, score, decision).
- Long-iter wakeups: ScheduleWakeup with delaySeconds tuned to expected iter time (avoid 300s exactly; <270 stays in cache, >=1200 amortizes cache miss). Re-enter via `/loop research <topic>` or autonomous-loop sentinel.
- Optional cron: user may pre-arm `/schedule` to kick this loop on a calendar (e.g. nightly), but the loop itself never schedules new prompts that ask the user anything.

## Stop conditions
- Iterations exhausted, OR
- Wall-clock cap reached, OR
- No-improve streak cap reached, OR
- Required invariants broken and not recoverable

## Never-stop clause
**You are autonomous. The human may be asleep, away, or expecting to wake up to a leaderboard. DO NOT pause to ask "should I continue?", "is this enough?", "good stopping point?". DO NOT ask anything. Side messages from the human get appended to `notes.md` and the loop keeps running. The ONLY things that stop the loop are: (a) one of the stop conditions below firing, (b) a forbidden path being touched (abort), (c) the human typing `stop` / `halt` / `abort`. Period. If you run out of ideas, think harder — re-read the in-scope files, look at referenced papers/docs, combine previous near-misses, try radical changes. Loop forever.**

## Notes
<free-form research context, prior art, hypotheses>
```

## `research-validation.<ext>` Skeleton

Validator must:
- Read whatever signal it needs (run.log, output files, in-process measurement).
- Print final line exactly: `RESULT_JSON: {"score": <number>, "valid": <bool>, "notes": "<string>"}`
- Exit 0 always (failure expressed via `valid: false`), unless harness itself broken.

### Python `.py`

```python
#!/usr/bin/env python
"""Validator. Prints RESULT_JSON: {...} on final line."""
import json, re, sys, pathlib

LOG = pathlib.Path("research/<topic>/run.log")

def main() -> int:
    if not LOG.exists():
        print('RESULT_JSON: {"score": 0.0, "valid": false, "notes": "no run.log"}')
        return 0
    text = LOG.read_text(errors="ignore")
    m = re.search(r"^val_bpb:\s*([\d.]+)", text, re.M)  # adapt to your metric line
    if not m:
        print('RESULT_JSON: {"score": 0.0, "valid": false, "notes": "metric not found"}')
        return 0
    score = float(m.group(1))
    print(json.dumps({"score": score, "valid": True, "notes": ""}, separators=(",", ":")).join(["RESULT_JSON: ", ""]))
    return 0

if __name__ == "__main__":
    sys.exit(main())
```

### TypeScript `.ts` (run with `bun` or `tsx`)

```ts
import { readFileSync, existsSync } from "node:fs";

const LOG = "research/<topic>/run.log";
function main() {
  if (!existsSync(LOG)) {
    console.log('RESULT_JSON: {"score":0,"valid":false,"notes":"no run.log"}');
    return;
  }
  const text = readFileSync(LOG, "utf8");
  const m = text.match(/^score:\s*([\d.]+)/m);
  if (!m) {
    console.log('RESULT_JSON: {"score":0,"valid":false,"notes":"metric not found"}');
    return;
  }
  const score = Number(m[1]);
  console.log(`RESULT_JSON: ${JSON.stringify({ score, valid: true, notes: "" })}`);
}
main();
```

### Shell `.sh`

```sh
#!/usr/bin/env sh
set -eu
LOG="research/<topic>/run.log"
if [ ! -f "$LOG" ]; then
  printf 'RESULT_JSON: {"score":0,"valid":false,"notes":"no run.log"}\n'
  exit 0
fi
SCORE=$(grep -E '^score:' "$LOG" | awk '{print $2}' | head -n1)
if [ -z "${SCORE:-}" ]; then
  printf 'RESULT_JSON: {"score":0,"valid":false,"notes":"metric not found"}\n'
  exit 0
fi
printf 'RESULT_JSON: {"score":%s,"valid":true,"notes":""}\n' "$SCORE"
```

### Go `.go`

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
	"regexp"
)

const logPath = "research/<topic>/run.log"

type Result struct {
	Score float64 `json:"score"`
	Valid bool    `json:"valid"`
	Notes string  `json:"notes"`
}

func emit(r Result) {
	b, _ := json.Marshal(r)
	fmt.Printf("RESULT_JSON: %s\n", b)
}

func main() {
	data, err := os.ReadFile(logPath)
	if err != nil {
		emit(Result{Valid: false, Notes: "no run.log"})
		return
	}
	m := regexp.MustCompile(`(?m)^score:\s*([0-9.]+)`).FindSubmatch(data)
	if m == nil {
		emit(Result{Valid: false, Notes: "metric not found"})
		return
	}
	var s float64
	fmt.Sscanf(string(m[1]), "%f", &s)
	emit(Result{Score: s, Valid: true})
}
```

### Rust `.rs`

```rust
use std::fs;

const LOG: &str = "research/<topic>/run.log";

fn emit(score: f64, valid: bool, notes: &str) {
    println!(
        "RESULT_JSON: {{\"score\":{},\"valid\":{},\"notes\":\"{}\"}}",
        score, valid, notes
    );
}

fn main() {
    let text = match fs::read_to_string(LOG) {
        Ok(t) => t,
        Err(_) => return emit(0.0, false, "no run.log"),
    };
    for line in text.lines() {
        if let Some(rest) = line.strip_prefix("score:") {
            if let Ok(v) = rest.trim().parse::<f64>() {
                return emit(v, true, "");
            }
        }
    }
    emit(0.0, false, "metric not found")
}
```

### JS `.mjs`

Same contract as `.ts`; drop type annotations.

### `validate.sh` wrapper (one-line invoker)

The `research` skill always runs `bash research/<topic>/validate.sh`. The wrapper picks the right runtime so the executor stays language-agnostic. Generate per language at scaffold time:

```sh
#!/usr/bin/env bash
# Pick the line for your validator language, delete the rest.
exec python   research/<topic>/research-validation.py
exec bun      research/<topic>/research-validation.ts
exec node     research/<topic>/research-validation.mjs
exec sh       research/<topic>/research-validation.sh
exec go run   research/<topic>/research-validation.go
exec cargo    run --quiet --manifest-path research/<topic>/Cargo.toml
```

Make it executable: `chmod +x research/<topic>/validate.sh`.

## Quality Checks Before Handoff

- [ ] `research-rules.md` has every section in schema
- [ ] Editable files listed; forbidden paths listed
- [ ] Validator command (`bash research/<topic>/validate.sh`) produces `RESULT_JSON:` line on dry run with current code as baseline
- [ ] Metric direction set (minimize | maximize)
- [ ] Time budget set (REQUIRED, fixed)
- [ ] Stop conditions present
- [ ] Simplicity criterion section present
- [ ] Never-stop clause present and forceful
- [ ] Branch convention names a fresh branch (does not exist yet)
- [ ] `.gitignore` excludes `run.log`, `results.tsv`, `worktrees/`, `pre-run.stash`
- [ ] Forbidden paths include `research/<topic>/` itself, with explicit exception: `results.tsv`, `run.log`, `notes.md` are write-allowed (logging carve-out)

## Handoff

Tell user: "Setup ready at `research/<topic>/`. Launch with `research` skill: it reads `research-rules.md` and starts the loop."

## Behavior Guardrails

- Do not generate target file. User owns target code.
- Do not run experiments. That is the `research` skill's job.
- Do not modify code outside `research/<topic>/` (and `.gitignore`).
- Do not skip validator dry-run if practical.
- Do not pick parallel mode silently when git worktree unavailable.
- Do not omit `Forbidden paths` or `Required invariants`.
