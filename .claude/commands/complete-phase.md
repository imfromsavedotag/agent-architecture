---
name: complete-phase
description: Run the review chain (test-gate → reviewer → [doc-updater + phase-bookkeeper]) for a completed phase; constitutional audit runs at /close-plan
argument-hint: "[plan-slug] [phase-number]"
arguments: [slug, phase]
allowed-tools: Read, Grep, Glob, Agent
---

The user wants to run the review chain on a completed phase.

## Resolve plan and phase

Arguments are optional. Resolve the plan-slug and phase number using this procedure:

1. **If `$slug` is provided**: look for `docs/plans/$slug/PLAN.md`.
2. **If `$slug` is empty or not provided**: scan `docs/plans/*/PLAN.md` for all plans, **skipping `docs/plans/completed_plans/`**. Read each plan's primer block. An "active" plan is one where "Next phase" is NOT "all complete".
   - If exactly one active plan exists, use it.
   - If multiple active plans exist, list them with their current phase and ask the user which one.
   - If no active plans exist, tell the user and stop.
3. **If `$phase` is provided**: use it.
4. **If `$phase` is empty or not provided**: read the primer block's "Next phase" field and use that value.

Once resolved, proceed with the plan-slug and phase number below.

## Instructions

1. **Verify the plan exists** at `docs/plans/<plan-slug>/PLAN.md`. If it does not exist, tell the user and stop.

2. **Check the primer block** — read the primer table in PLAN.md. If the resolved phase is already marked complete (i.e., "Phases complete" count already includes this phase, or "Next phase" points past it), tell the user this phase is already complete and stop. Do not re-run the chain on a completed phase.

3. **Detect code modifications** — Read the phase checklist in PLAN.md. Scan all checklist items for any that create or modify source code files: Python (`.py`), JavaScript/TypeScript (`.js`, `.ts`, `.tsx`), HTML templates (`.html`, `.jinja2`), CSS/SCSS (`.css`, `.scss`), or SQL migration scripts that add or modify schema objects. If **no checklist item** creates or modifies a source code file (i.e., the phase only updates documentation, configuration YAML, JSON data files, or similar non-code assets), **skip steps 4 and 5** — this is a documentation/config-only phase and does not require a test run or code review. Note this skip in your final summary.

4. **Gate 1 — Test Gate**: Spawn the `test-gate` subagent using the Agent tool:
   - subagent_type: `test-gate`
   - Pass the plan-slug and phase number
   - The test-gate will create `docs/plans/<plan-slug>/phase-<phase>-tests.md`

   After the subagent completes, read its Result block. If `blocking: true`, report the test-gate summary to the user and **stop**. Do not proceed to the reviewer.

5. **Gate 2 — Code Review**: Spawn the `reviewer` subagent using the Agent tool:
   - subagent_type: `reviewer`
   - Pass the plan-slug and phase number
   - The reviewer will create `docs/plans/<plan-slug>/phase-<phase>-review.md`

   After the subagent completes, read its Result block. If `blocking: true`, report the reviewer summary to the user and **stop**. Do not proceed to phase completion.

6. **All gates passed — Phase Completion**: Spawn `doc-updater` AND `phase-bookkeeper` simultaneously using the Agent tool. Both receive the plan-slug and phase number. They operate on non-overlapping file sets (doc-updater writes to project documentation files; phase-bookkeeper writes to CHANGELOG, PLAN.md, and files-changed.md) and must run in parallel — send both Agent tool calls in the same message.

   After both complete, read their Result blocks and report to the user:
   - Summary of gates run (test, review if applicable; note if test/review were skipped for a no-code phase)
   - What documentation was updated (from doc-updater)
   - Where the primer now points (next phase or "all complete") (from phase-bookkeeper)

## Important

- The chain is **serial with short-circuit**: each gate is a prerequisite for the next. Never run a later gate if an earlier one has blocking findings.
- If the phase made no source code modifications, the test-gate and reviewer are skipped entirely. doc-updater and phase-bookkeeper still run (in parallel).
- **Constitutional audit does not run here.** It runs at `/close-plan` time, covering all phases together, so violations visible only across the full plan are not missed.
- If any gate halts the chain, clearly tell the user which gate failed, what the blocking findings are, and that they need to fix the issues before re-running `/complete-phase`.
- This skill does NOT commit anything. Commits happen at plan closeout via `/close-plan`.
