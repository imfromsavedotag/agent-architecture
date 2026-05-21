---
name: kickoff-phase
description: Run a pre-execution review of a specific phase before implementation begins
argument-hint: "[plan-slug] [phase-number]"
arguments: [slug, phase]
allowed-tools: Read, Grep, Glob, Write, Agent
---

The user wants to kick off a phase for pre-execution review.

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

1. Verify the plan exists at `docs/plans/<plan-slug>/PLAN.md`. If it does not exist, tell the user and stop.

2. Spawn the `plan-inspector` subagent using the Agent tool:
   - subagent_type: `plan-inspector`
   - Pass the plan-slug and phase number
   - The inspector will create `docs/plans/<plan-slug>/phase-<phase>-kickoff.md`

3. After the inspector completes, read the generated kickoff and present it to the user **preserving full context**. Do NOT compress, summarize, or reduce the inspector's output to bullet points or counts. The inspector writes prose paragraphs with deliberate contextualization — that context is what the user needs to make decisions. Present:
   - The **Understanding** section in full (or near-full) — this is how the user confirms strategic alignment
   - Each **Concern** with its full explanatory paragraph and heading — not just a one-line summary
   - Each **Clarification** with its full context paragraph — the user needs the stakes and tradeoffs explained to answer well
   - The inspector's Result block status

4. If there are clarifications needed, present them as part of the flow above — they should already be fully contextualized from step 3. Ask the user to address each one.

5. **Stop here.** This is **Human Gate 2**: the user reviews the kickoff, answers clarifications, and gives explicit approval before implementation begins. Do NOT proceed to implementation automatically.

6. **After the user says "proceed"**: spawn the implementation agent with `run_in_background=true`. The agent prompt MUST include these explicit checkpointing instructions — copy them verbatim into the prompt:

   > **Checkpointing is mandatory.** At the start of your work, register one task per subtask using TaskCreate (status: not_started). Mark each task `in_progress` when you begin it and `completed` when done. After completing each subtask, also append one line to `docs/plans/<slug>/STATE.md` in this format:
   > `[HH:MM] Subtask <letter> complete — <one sentence summary>. Subtasks done: X of Y.`
   > These are the only status signals visible to the orchestrator while you run in the background.

   After spawning, tell the user:
   - The agent is running in the background
   - They can ask "how is it progressing?" at any time — you will read STATE.md to report
   - Or they can run `TaskList` to see per-subtask completion status

7. **After the background agent completes**: Do NOT mark checklist items as complete, do NOT advance the primer block, do NOT update `docs/CHANGELOG.md` with completion entries, and do NOT perform any other phase-completion activity. These are the responsibility of `/complete-phase`, which runs the full review chain (test-gate → reviewer → doc-updater + phase-bookkeeper). Instead, tell the user that implementation is done and suggest they run `/complete-phase` when ready.
