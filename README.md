# agent-architecture

---

## The problem this addresses

You describe a change. The agent writes code. The code looks right. Two weeks later something breaks in a place you weren't watching, and you trace it back to a decision the agent made on session three that you have no record of.

Or: you start a session, catch the agent up on where things stand, it produces something reasonable. You start the next session, catch it up again. On the fourth session you realize the agent has drifted slightly in each one, and you've been course-correcting the drift instead of building.

Or: you're running a six-hour implementation session and somewhere around hour four the output quality degrades — not catastrophically, just subtly — and you don't notice until you're reviewing the diff at midnight.

These are not tool failures. They are predictable consequences of using AI tools that forget between sessions, without any structure to compensate. This repository contains a methodology designed around that reality.

---

## What the methodology does

The core structure is a split between planning and building. A planning session — run as a separate conversation, away from the coding tool — is structured as a dialog: the planner pushes back on framing, names assumption gaps, and disagrees plainly when scope or premises are wrong. The session produces a phased plan and a briefing document. The briefing carries the architect's intent and the rules the agent should follow into the coding session, without requiring the agent to have been present for the planning conversation. When the coding session ends and memory resets, those documents are the source of truth.

Phases are sized to complete within a single session, so the agent never approaches its limits during a build. The review chain runs automatically after each phase: test gate, code reviewer, constitutional auditor, phase completer. The constitution pre-decides implementation choices — config before code, isolated environments, every decision recorded — so those choices are not relitigated mid-build. Completed plans accumulate a learnings file; the planner reads it before drafting new plans, so recurring failure modes stop recurring.

The automation layer is built natively for Claude Code — the skills, agents, and session hooks rely on Claude Code's architecture. The core planning philosophy can be applied manually in any environment, but the automated review chain requires Claude Code. The methodology has already been rearchitected for portability: the constitution is domain-neutral with an extension model, onboarding is interview-driven and produces configured artifacts for any project, and a worked example of port planning to a different platform is included in the repository. Reducing the Claude Code-specific automation dependency for other platforms is ongoing work — portability is treated as proof of the methodology's value, not an afterthought.

The plan history behind these claims is documented in `docs/velocity_reference.md`. It is not illustrative — it is the actual record from the project this methodology was built for, with real durations, phase counts, and failure modes.

---

## Repository tour

### Top-level files

- **`BOOTSTRAP.md`** — start here if you are adopting the methodology for a new project; explains the workflow and entry point
- **`docs/constitution.md`** — the 9-article base constitution (v3.0.0); pre-decides implementation choices so they are not re-debated per phase
- **`docs/save_ag_extension.md`** — a worked example of the extension model; shows how to author project-specific articles that supplement the base nine
- **`.aiag/config.yaml.example`** — fully annotated adopter configuration file with all 14 keys; copy to `.aiag/config.yaml` or generate via `/onboard`
- **`docs/velocity_reference.md`** — plan histories from the original project showing how long different kinds of work took, broken down by phase; use for estimation
- **`docs/EVALUATION.md`** — written for an agent as primary reader; covers appropriate use cases, misfit cases, and when not to adopt

### Agents (`.claude/agents/`) — 10 files

1. **`planner.md`** — drafts phased implementation plans from a described objective; reads learnings before planning
2. **`plan-inspector.md`** — pre-execution reviewer; reads a phase before implementation begins and surfaces assumption gaps
3. **`reviewer.md`** — post-implementation code reviewer; checks correctness, coupling, and documentation currency
4. **`constitutional-auditor.md`** — verifies each phase against the project constitution; runs at plan close
5. **`test-gate.md`** — runs the test suite before the reviewer sees the code; short-circuits on failure
6. **`phase-bookkeeper.md`** — updates CHANGELOG and advances the plan's status block after each phase completes
7. **`doc-updater.md`** — routes documentation updates to the correct guide files after code changes
8. **`retrospective.md`** — produces the plan retrospective document at close; surfaces what went differently from the plan
9. **`learnings-consolidator.md`** — identifies patterns across retrospectives worth adding to the project's long-term memory file
10. **`plan-closer.md`** — orchestrates the close sequence: constitutional audit, retrospective, learnings consolidation, commit staging

### Skills (`.claude/commands/`) — 6 files

1. **`plan.md`** — `/plan` — starts a planning session; produces a phased plan with briefing documents in `docs/plans/<name>/`
2. **`kickoff-phase.md`** — `/kickoff-phase` — runs the plan-inspector before implementation begins; surfaces concerns before any code is written
3. **`complete-phase.md`** — `/complete-phase` — runs the review chain (test gate → reviewer → doc-updater → phase-bookkeeper) after a phase is built
4. **`close-plan.md`** — `/close-plan` — runs the close sequence after all phases are done; stages the commit for architect approval
5. **`onboard.md`** — `/onboard` — 14-question interview that generates `.aiag/config.yaml` for a new project; configures optional features
6. **`wrap-change.md`** — `/wrap-change` — post-implementation wrapper for ad-hoc changes made outside a formal plan; runs a code review, routes documentation updates, rebuilds the knowledge graph, and stages the commit. This is how the codebase stays self-documenting and reviewed for quick fixes and debug sessions that don't warrant a full plan — the same underlying discipline, without the two-hour planning investment

### Templates (`.claude/templates/`) — 8 files

1. **`PLAN.md`** — canonical plan document structure with status block, phase sections, and acceptance criteria
2. **`STATE.md`** — per-plan state file; tracks subtask progress, decisions made, and what you need to pick up where you left off
3. **`phase-kickoff.md`** — template for the plan-inspector's pre-execution review document
4. **`phase-review.md`** — template for the reviewer's post-implementation code review document
5. **`phase-tests.md`** — template for the test-gate's results document
6. **`phase-constitutional.md`** — template for the constitutional auditor's findings
7. **`retrospective.md`** — template for the plan retrospective document
8. **`closeout.md`** — template for the plan closeout summary

### Worked example

**`examples/cursor_port_planning/`** — three files showing how to apply the methodology's planning discipline to a platform adaptation question (porting from Claude Code to Cursor). Demonstrates the three-category framework: direct translation, adaptation, and deferral. Not a finished port plan — an illustrative exercise showing how the methodology reasons about its own portability.

---

## Adoption paths

**Path 1: Fresh project via interview**

Run `/onboard` in Claude Code. The interview asks about your project structure, whether you have a constitution document, which optional features you want to enable, and where your documentation lives. It writes `.aiag/config.yaml` with your answers. The whole process takes about five minutes. Start with a small piece of work and follow the flow from `/plan` through `/close-plan` — one plan teaches you more than reading the documentation.

This path works equally well for established codebases and for foundation MVPs where the intent from day one is a production system. Starting with structure costs less than retrofitting it later.

**Path 2: Fork and adapt**

Clone the repository into your project root. Replace the constitution articles' identity references and adapt any domain-specific articles to your project. Set up `.aiag/config.yaml` from the example. Run `/onboard` afterward to validate the configuration and initialize the learnings file. The `examples/cursor_port_planning/` directory demonstrates how to reason about adapting the methodology to a different coding tool.

The methodology is designed to be modified. If the review process is more than your work warrants, simplify it. If you don't need eleven articles, write fewer. The shape matters more than the specifics.

**Not all work requires a formal plan.** Quick fixes, debug sessions, and one-file changes run through `/wrap-change` instead — a lightweight gate that enforces documentation and knowledge graph updates before anything is committed. The self-documenting guarantee holds for ad-hoc work for the same reason it holds for planned work: the gate is mechanical, not a matter of remembering.

---

## Authorship

Built by Ian Murdock. See [AUTHOR.md](AUTHOR.md) for background.

The methodology was developed for and is in active use at [save.ag/why](https://save.ag/why).
