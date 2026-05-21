# Getting Started with the Planning Methodology

This methodology is a structured system for planning, reviewing, and completing software changes. It runs inside Claude Code as a set of skills and subagents. When you start a change, you open a plan; when you finish, you run a review chain that checks correctness, documentation currency, and compliance against your project's principles. Over time, the methodology accumulates learnings from completed plans and uses them to improve future planning. The core discipline is: no code gets merged without a reviewer pass, no plan phase gets closed without checkpointed state, and no agent carries hardcoded assumptions about your project.

---

## Prerequisites

- Claude Code installed and connected to your project directory
- A project with source code (any language or stack)
- Basic familiarity with Git (the methodology stages commits but never pushes without your approval)

---

## Getting Started

Run `/onboard` in Claude Code to configure the methodology for your project.

```
/onboard
```

This starts a short interview. You will answer questions about your project and the methodology will write its configuration file. The whole process takes about five minutes.

---

## What `/onboard` Does

The interview asks about:

- Your project name and primary language(s)
- Whether you have a principles or constitution document (a file that states your project's non-negotiable rules — the methodology will audit compliance against it during plan closeout)
- Whether you want to enable the knowledge graph substrate (builds a reverse-dependency graph of your codebase — enables the methodology to warn about callers before you modify a module)
- Whether you want to enable doc-routing (maps source files to their documentation targets — enables automatic documentation updates when you change code)
- Your database connection details if you have one (used only by the optional DB knowledge graph builder)
- Where your documentation directories live

After the interview, `/onboard` writes the methodology configuration file with your answers and optionally initializes your learnings file.

---

## What to Expect After Onboarding

Once configured, the typical workflow is:

1. Describe a change you want to make and run `/plan <slug>` — the planner produces a phased implementation plan in `docs/plans/<slug>/`
2. Run `/kickoff-phase <slug> 1` before you implement — a pre-execution review catches assumption mismatches before any code is written
3. Implement the phase
4. Run `/complete-phase` — triggers the review chain: test gate, reviewer, doc-updater, and phase-bookkeeper
5. Repeat for each phase until the plan is complete
6. Run `/close-plan` — runs the retrospective, updates learnings, and stages the commit for your approval

After a few plans, the learnings file accumulates patterns. The planner reads it before drafting new plans so that recurring issues stop recurring.

---

## Advanced Features

Two substrate features are opt-in during onboarding:

**Knowledge graph** (`substrate_features.knowledge_graph: true`): After each change, the methodology rebuilds a reverse-dependency graph of your codebase. When you modify a module, the reviewer is told which other modules depend on it. Skipping this means tracing call chains manually.

**Doc-routing** (`substrate_features.doc_routing: true`): Source files are mapped to their documentation targets. When you change a file, the doc-updater knows which guide pages to update. Skipping this means keeping documentation in sync is entirely manual work.

Both features require a small amount of project-specific setup (a build script for the graph, a mapping file for doc-routing). The interview explains the setup cost before asking whether you want to enable them.

---

## Portability Note

This methodology was designed to be adopted by any software project, not tied to a particular codebase or domain. If you are porting the methodology from another project or evaluating it for a new context, a worked port-planning example is in `examples/cursor_port_planning/`. It walks through how to adapt the configuration, constitution, and substrate setup for a project with different conventions.
