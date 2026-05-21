---
name: plan
description: Create a structured implementation plan from objectives described in conversation
argument-hint: [slug] [executor]
allowed-tools: Read, Grep, Glob, Write, Agent
---

The user wants to create a new implementation plan with slug `$slug`, to be implemented by executor `$executor`.

## Instructions

### 1. Validate inputs

- `$slug` must be a valid directory name (lowercase, dashes, no spaces)
- `$executor` must be a known model identifier (`sonnet-4-5`, `haiku-4-5`, `opus-4-7`) or a budget override in tokens (e.g. `60k`)
- If `$executor` is missing, ask the user before proceeding. Phase sizing is meaningless without a target.

### 2. Gather the context

Look at the conversation above for the user's description of what they want to accomplish. This is the objective context the planner needs.

### 3. Spawn the planner subagent using the Agent tool

Pass:
- plan-slug: `$slug`
- executor: `$executor`
- objective context: a summary of what the user described in the conversation

The planner will create:
- `docs/plans/$slug/requirements.md` (verbatim objectives, for traceability)
- `docs/plans/$slug/PLAN.md` (the phased plan)
- `docs/plans/$slug/STATE.md` (initialized for phase 1, empty progress)

### 4. Read the generated PLAN.md and print a summary

Include:
- Number of phases
- Per-phase context budget: total / working budget / headroom
- Any phases that were split, and why
- Key reading list highlights
- Confirmation that requirements.md and STATE.md were created
- The planner's Result block status

### 5. Gate 1b — Gap Analysis

Before presenting the plan for approval, evaluate it against the actual codebase. This catches assumption mismatches that are invisible in the abstract.

**Codebase reality checks:**

- **Data shapes**: Query the actual data the plan will operate on — field names, value distributions, counts, schemas. Plans drafted from conversation context often assume data structures that don't match reality.
- **Hot path latency**: Trace the critical path for any new per-request work. Per-item LLM calls that look clean in a checklist can blow the latency budget when multiplied by actual item counts.
- **Existing gate/filter interactions**: Check whether the codebase already has gates, demotions, or bypass logic that the plan's new code paths must respect. Missing these creates contradictory behavior.
- **Artifact preservation**: If later phases depend on artifacts from earlier work (baselines, diagnostics), verify those artifacts will be preserved.
- **Template/UI completeness**: If the plan adds data that reaches the user, verify the full delivery path — backend through to template and JS handler.

**Plan structure checks:**

- **Context budget integrity**: For each phase, verify `Total × 1.2 ≤ Working budget`. Any phase that fails this check should have been split — flag as a planning bug requiring regeneration, not a fix in place.
- **Reading list tagging**: Every reading list entry must be tagged `[full-read]` or `[search/grep]`. Untagged entries cannot be cost-estimated correctly and indicate the planner cut a corner.
- **Subtask boundaries present**: Every phase must have at least one subtask boundary with a checkpoint block. Phases with a single monolithic checklist provide no resumability and defeat the budget-aware design.

Report any material gaps found. If gaps exist, update the plan to address them before presenting for approval. If no gaps are found, note that the gap analysis passed.

### 6. Remind the user

- Review `docs/plans/$slug/PLAN.md` before proceeding
- When ready, use `/kickoff-phase $slug 1` to begin the pre-execution review of Phase 1
- This is **Human Gate 1**: the plan must be approved before any phase begins
