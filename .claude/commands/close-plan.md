---
name: close-plan
description: Close a completed plan — run retrospective, stage commit, and halt for human approval
argument-hint: "[plan-slug]"
arguments: [slug]
allowed-tools: Read, Grep, Glob, Agent
---

The user wants to close a completed plan.

## Resolve plan

The argument is optional. Resolve the plan-slug using this procedure:

1. **If `$slug` is provided**: look for `docs/plans/$slug/PLAN.md`.
2. **If `$slug` is empty or not provided**: scan `docs/plans/*/PLAN.md` for all plans, **skipping `docs/plans/completed_plans/`**. Read each plan's primer block. A "closeable" plan is one where ALL phases are complete (i.e., "Next phase" is "all complete").
   - If exactly one closeable plan exists, use it.
   - If multiple closeable plans exist, list them and ask the user which one.
   - If no closeable plans exist, tell the user (and mention if there are active plans that still have incomplete phases) and stop.

Once resolved, proceed with the plan-slug below.

## Instructions

1. **Verify the plan exists** at `docs/plans/<plan-slug>/PLAN.md`. If it does not exist, tell the user and stop.

2. **Check completeness** — Read the primer block. Verify that "Next phase" is "all complete". If any phases remain incomplete, tell the user which phases are not done and that they must complete all phases before closing. Stop.

3. **Re-run check** — Before running the constitutional audit, check whether `docs/plans/<plan-slug>/plan-constitutional.md` already exists. If it does, a prior `/close-plan` run was halted by blocking constitutional findings. In that case:
   - Read `docs/plans/<plan-slug>/files-changed.md`. For each phase section that lists actual files (not `(no source code changes)`), that phase is code-modifying.
   - For each code-modifying phase: spawn `test-gate` and then `reviewer` sequentially. This re-validates any code fixes the user made in response to the prior audit.
   - If a test-gate or reviewer run comes back blocking, report the findings and **stop** — do not proceed to the constitutional audit.

4. **Gate 1 — Constitutional Audit** — Spawn the `constitutional-auditor` subagent once for the entire plan:
   - subagent_type: `constitutional-auditor`
   - Pass the plan-slug only (no phase number)
   - The auditor reads `files-changed.md` for the complete inventory of changed files and writes `docs/plans/<plan-slug>/plan-constitutional.md`

   After the subagent completes, read its Result block. If `blocking: true`, report all blocking findings to the user and **stop**. Tell the user: "Fix these issues, then re-run `/close-plan`. The next run will re-validate tests and code review for affected phases before re-auditing."

   If the audit passes, proceed.

5. **Gate 2 — Retrospective**: Spawn the `retrospective` subagent using the Agent tool:
   - subagent_type: `retrospective`
   - Pass the plan-slug
   - The retrospective will create `docs/plans/<plan-slug>/retrospective.md` and append to `.claude/learnings.md`

   After the subagent completes, read its Result block. If `status: fail`, report the summary to the user and **stop**.

6. **Learnings Consolidation** — Spawn the `learnings-consolidator` subagent:
   - subagent_type: `learnings-consolidator`
   - Pass the plan-slug — the consolidator uses it to write `docs/plans/<plan-slug>/memory-recommendations.md`

   The consolidator self-checks both thresholds (consolidation and staleness) and skips the respective step if not needed. After it completes, read its Result block. If `status: fail`, report the issue to the user but **continue** — consolidation failures do not block plan closeout.

   **Immediately after the consolidator completes**: Read `docs/plans/<plan-slug>/memory-recommendations.md` now and hold its full content in context. The plan-closer (step 7) will delete this file as a working artifact — reading it here ensures the content survives for the step 8 interview regardless of deletion.

7. **Gate 3 — Plan Closer**: Spawn the `plan-closer` subagent using the Agent tool:
   - subagent_type: `plan-closer`
   - Pass the plan-slug
   - The plan-closer will delete phase artifacts, stage files, and create `docs/plans/<plan-slug>/closeout.md`

   After the subagent completes, read its Result block. If `status: fail`, report the summary to the user and **stop**.

   **Note**: The plan-closer moves the plan directory from `docs/plans/<plan-slug>/` to `docs/plans/completed_plans/<plan-slug>/`. Read the closeout from the new location.

8. **Present for human approval** — Show the user:
   - The retrospective summary (patterns found, learnings appended)
   - The consolidation result (consolidated or below threshold)
   - **Memory recommendations interview** — If `docs/plans/completed_plans/<plan-slug>/memory-recommendations.md` exists, read it and present each recommendation as a numbered item in interview format. For each item, display its type (new entry / update / prompt flag), the suggested file name or target, and the draft content. Then ask the user to respond to each one with: **a** (accept — you will create or update the memory file), **r** (reject), or a short comment. Present all items at once so the user can respond in a single message. After the user responds, note their decisions but do NOT create or modify memory files — that remains the user's action.
   - The commit message from closeout.md (now at `docs/plans/completed_plans/<plan-slug>/closeout.md`)
   - The list of staged files from closeout.md
   - Instructions: "Review the staged diff with `git diff --cached`. If everything looks good, run `git commit` with the message above. If not, unstage and fix."

9. **Stop here.** This is **Human Gate 3**: the user reviews the staged diff and commit message, then commits manually. Do NOT run `git commit`.

## Important

- The chain is **serial**: constitutional audit → retrospective → learnings consolidation → plan-closer. The retrospective reads constitutional artifacts; the plan-closer deletes all phase artifacts. Order is mandatory.
- If the constitutional audit blocks, do NOT proceed to the retrospective — report findings and halt. The constitutional artifacts remain in place so the next run detects the prior failure and re-validates test and review before re-auditing.
- If the retrospective fails, do NOT proceed to the plan-closer — the artifacts would be lost.
- This skill does NOT commit. The human commits after reviewing the staged diff.
- The plan-closer stages only plan-owned files. If the user has other uncommitted changes, those remain unstaged.
