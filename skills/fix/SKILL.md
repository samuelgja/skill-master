---
name: fix
description: Use when fixing bugs or regressions with a strict test-first default. Trigger for runtime errors, failing behavior, data mismatches, flaky tests, and production defects. Start with a failing test whenever possible, then apply one minimal root-cause patch with verification evidence.
---

# Fix

Ship a verified bug fix fast, with test evidence.

## Quick Path

1. Load relevant memory first (use-memory flow, local by default).
2. Start with one failing test whenever tests are available.
3. Apply one minimal root-cause fix and verify.

## Hard Rules

1. Do not patch from a vague symptom.
2. Default to failing-test-first.
3. One bug, one failing test, one minimal fix.
4. No "fixed" claim without passing evidence.
5. After two failed patch attempts, stop patching and switch to deep localization.
6. When a bug is identified, say it clearly to the user:
- Preferred: `I found the bug.` (when backed by a failing test or direct evidence)
- If evidence is strong but not yet fully proven: `I likely found the bug in <area>; I will confirm with a failing test.`
- Avoid absolute certainty language before evidence.
7. If memory exists, apply relevant memory constraints before choosing tools, test scope, or patch strategy.
8. Do not ask the user to say "continue" after intermediate outputs; continue the fix loop unless blocked.

## Step -2: Memory Load (Required)

Before Step -1, run the use-memory behavior:

1. Read local memory from `docs/memory/memory.md` (and global only if explicitly requested).
2. Select the most relevant 3-7 lines for this bug.
3. Convert to short action rules for this fix.
4. Continue immediately into bug-fix flow.

If memory file is missing, continue without blocking.

## Step -1: Task Context Check (Fast)

Before Step 0, quickly check whether there is an active task/plan for this bug.

- If no active task exists: continue normal bug-fix flow.
- If active task exists: read only the minimum needed:
  - task brief/objective
  - current phase/task line
  - acceptance criteria relevant to the bug

Do not deep-read the full plan unless blocked.

## Step 0: Bug Brief (Required)

Write a 5-line brief before coding:

- Symptom
- Expected behavior
- Reproduction
- Scope / likely module
- Unknowns and assumptions

If expected behavior is unclear, inspect tests/docs/history first, then ask one precise question.
If a question is needed, format it as a pickup list:
- `Q1: <what decision is needed>`
- `Pick one:`
- `1. <Option A> - <impact>`
- `2. <Option B> - <impact>`
- `3. Other - <one-line answer>`

## Step 1: Hypotheses (60 seconds)

List 3 hypotheses. For each:

- Why it explains the symptom
- Fastest falsification check

Pick the highest-impact, fastest-to-disprove hypothesis first.

## Step 2: Route

Choose one:

- FAST route (default): expected vs actual is clear and a failing test can be written now.
- DEEP route: reproduction is unstable, expected behavior is unclear, or no reliable failing test can be written yet.

## Speed Accelerators (Use When Applicable)

1. Minimize the reproducer first. Shrink inputs/steps to the smallest failing case before patching.
2. Run the smallest test slice first, then expand:
- `pytest path/to/test.py::test_name -x`
- `pytest --lf`
- `jest --runTestsByPath <file>`
- `jest --findRelatedTests <changed-files>`
3. Prefer deterministic checks over broad reruns while iterating.
4. If this is a recent regression and git policy allows it, use `git bisect` (or `git bisect run <test-cmd>`) to find the first bad commit quickly; narrow scope with `git bisect start <bad> <good> -- <paths>` when possible.

## FAST Route (Test-First Loop)

1. Write one failing test that encodes the bug.
2. Run only that test and confirm assertion failure (not setup failure).
3. Apply the smallest root-cause fix.
4. Re-run the same test until green.
5. Run nearby tests.
6. Run full checks (tests, lint, types) when available.

If attempt 1 fails, run one more informed patch.
If attempt 2 fails, switch to DEEP route.

## Failing Test Approach (Required Default)

Write somehow tests and use this exact order:

1. Name test by observable behavior, not implementation detail.
2. Encode one bug expectation in one assertion group.
3. Verify test fails for the target reason.
4. Patch only the root-cause area proven by the failing test.
5. Re-run same test to green, then broaden test scope.

A fix is not complete if the test never failed first.

## DEEP Route (Localize Then Test)

1. Reproduce with the smallest reliable trigger.
2. Localize using probes/logs/binary search across layers.
3. State one falsifiable claim: `Bug is X because Y`.
4. Convert claim into a failing test.
5. Apply one minimal fix.
6. Verify using FAST route steps 4-6.

## Flaky/Intermittent Bug Shortcut

If failure is intermittent:

1. Prove flakiness first with repeated targeted runs.
2. Remove timing randomness in tests (avoid sleep-based assertions when possible).
3. Stabilize reproduction, then return to FAST route with one failing deterministic test.

## Exceptions (Still Strict)

Use temporary non-test evidence only when tests are blocked by infrastructure limits. In that case:

1. Capture reproducible runtime evidence (logs/output/trace).
2. Apply minimal fix.
3. Capture matched post-fix evidence.
4. Add backlog note for missing automated test.

Do not close the bug as fully resolved without a test plan.
Return to failing-test approach as soon as infrastructure allows.

## Output Format

Return:

- Bug brief
- Chosen route + one-line reason
- Failing evidence -> passing evidence
- Minimal patch summary
- Residual risk
- Follow-up checks

## Multiple Bugs

1. Track one item per bug.
2. Check for shared root cause.
3. If independent, fix in parallel only when files/flows do not overlap.
4. Keep separate failing tests and verification per bug.

## Escalation

After 3+ total failed attempts on one bug, stop and report:

- Attempts and why each failed
- Patch vs redesign recommendation
- Smallest user decision needed next
