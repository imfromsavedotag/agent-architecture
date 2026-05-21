# Foundation — Cursor Port Planning

## Purpose

This document is the Phase 1 output of a hypothetical plan to port the planning methodology from Claude Code to Cursor. It applies the methodology's reconnaissance-first discipline to the platform adaptation question: before any configuration files are created or methodology documents are copied, we categorize what we are working with.

The three-category framework:
1. **Direct translation** — concepts and mechanisms that carry over without structural change
2. **Adaptation required** — concepts that survive but need their implementation replaced
3. **Deferral** — substrate features that a Cursor adopter would reasonably skip on first adoption

---

## Category 1: What Translates Directly

### The gate structure and review chain

The methodology's core value is in its serial gate structure: no phase advances without a reviewer pass, no plan closes without a constitutional audit, no implementation starts without a kickoff review. These are conceptual gates, not Claude Code features. Cursor does not prevent you from running the same discipline:

- A pre-execution review (`/kickoff-phase` equivalent) is a prompt-driven conversation, not a platform feature
- A post-phase review chain (test → reviewer → auditor → bookkeeper) is four sequential conversation turns, not a subagent protocol
- Human approval gates are moments where you pause and confirm, not platform primitives

A Cursor adopter would implement these as a documented workflow (the equivalent of `docs/workflow/workflow_automation.md`) and follow them as a discipline rather than automating them. The gate structure is fully portable; the automation is not (see Category 2).

**Assessment**: Translate directly. The discipline is the value; the automation is convenience.

### The constitutional audit discipline

The constitution (`docs/constitution.md`) is a Markdown file. The constitutional auditor is a prompt that reads the file and checks a set of files against each article. In Cursor, this is a manual prompt invocation: "Read `docs/constitution.md` and check the following changed files against each article." The auditor agent (`constitutional-auditor.md`) becomes a prompt template the adopter pastes into a conversation.

The nine-article structure (config-first, filesystem-first, auditability, compliance, etc.) is domain-agnostic and applies to any software project. A Cursor adopter would keep the same constitution text, potentially add a project-specific extension document, and run the audit manually at plan closeout.

**Assessment**: Translate directly. Replace automated invocation with a manual prompt.

### The learnings loop

The methodology's learnings loop is: retrospective → consolidation → memory promotion. In Claude Code, the learnings-consolidator agent automates the consolidation step. In Cursor, this becomes:

1. Retrospective document produced manually at plan close (same format)
2. Human reviews the retrospective and adds notable patterns to `.claude/learnings.md` (or equivalent)
3. Human reads learnings before starting each new plan

The value of the loop is the discipline of capturing and applying patterns, not the automation. The learnings file is a Markdown document; Cursor can read it just as Claude Code can.

**Assessment**: Translate directly with manual execution of the consolidation step.

### The phased plan structure

A `PLAN.md` document with sequential phases, each with a checklist of subtasks and verify criteria, is a Markdown artifact. A `STATE.md` document tracking completion is the same. These are documents, not platform features. A Cursor adopter creates the same files in the same location and uses them the same way.

The plan-bookkeeper pattern (updating the primer block, ticking checklist items) is mechanical but platform-independent. In Cursor it is done by hand or by a prompted conversation.

**Assessment**: Translate directly.

### The configuration-first principle (Article I)

Config-first is a code-authoring discipline: no hardcoded tunable values in Python, every threshold in `config.yaml`. This has no dependence on Claude Code. A Cursor adopter follows the same rule; the reviewer checks for violations. This is arguably the most directly portable principle in the entire methodology because it is language-level, not tooling-level.

**Assessment**: Translate directly.

---

## Category 2: What Needs Adaptation

### Subagent prompts → Cursor rules or prompt templates

Claude Code's subagent system (`.claude/agents/*.md` files) uses a platform-specific agent invocation mechanism. When the planner runs, it loads a system prompt from `planner.md`; when the reviewer runs, it loads from `reviewer.md`. This is a Claude Code feature — Cursor does not have a direct equivalent.

The adaptation options are:

**Option A — Cursor rules (`.cursorrules` or `.cursor/rules/`)**: Cursor supports per-project rules that inject context into every conversation. A Cursor adopter could convert each agent's system prompt into a Cursor rule, or a set of rules organized by role. The limitation is that Cursor rules apply globally, not per-invocation — the adopter would not get a clean "planner mode" vs. "reviewer mode" unless they use separate Cursor workspaces or rule-set switching.

**Option B — Saved prompt templates**: The adopter maintains a directory of prompt templates (e.g., `prompts/planner.md`, `prompts/reviewer.md`) and pastes the appropriate template into a Cursor conversation at the start of each role-specific session. This is more manual but preserves the clean role separation. It is also transparent: there is no magic invocation mechanism, just text the adopter controls.

**Option C — Hybrid**: Key agents (planner, reviewer, constitutional-auditor) become Cursor rules that are always active; role-switching is done by starting a new Cursor conversation and noting which "hat" is being worn in the initial prompt.

**Assessment**: Requires adaptation. Option B (saved prompt templates) is the lowest-risk translation because it preserves the content of the agent prompts without depending on platform features. Option A is more integrated but risks rule conflicts and loss of role clarity. The recommended approach for a first Cursor port is Option B.

### Skills/commands → Cursor task definitions or custom commands

Claude Code skills (`.claude/commands/*.md` files) are invoked with `/skill-name` in conversation. They are structured prompts with frontmatter that declares tools and a description. Cursor's equivalent depends on the version:

- Cursor has a concept of custom commands (`.cursor/commands/`) in some versions
- More reliably, Cursor supports task definitions in `tasks.json` (VS Code-style) for invoking shell commands
- For prompt-driven skills (like `/wrap-change`, `/plan`), the closest Cursor analog is a saved prompt that the adopter copies into a conversation

The adaptation is: prompt-driven skills become saved prompt templates (same as agents above); shell-invocation skills become Cursor tasks or Makefile targets.

For the most commonly used skills:
- `/wrap-change` → a Makefile target or shell script that runs the knowledge graph rebuild and stages files, plus a saved prompt template for the doc-update conversation
- `/plan` → a saved prompt template
- `/onboard` → a one-time shell script that writes `.aiag/config.yaml` based on prompts or a config template

**Assessment**: Requires adaptation. Shell-based steps become scripts; conversation-based steps become saved prompt templates.

### Hooks → Cursor event system equivalents

Claude Code's hooks (PostToolUse and Stop hooks in `.claude/settings.local.json`) fire automatically after tool use and session end. These drive the PostToolUse annotation tracker (which files were modified?) and the Stop hook (which modified files have `# doc:` annotations?). Cursor does not have a comparable hooks system in the same sense.

Adaptations:
- The PostToolUse annotation tracker becomes a manual step: after implementing a change, the adopter runs a script (`scripts/check_doc_annotations.py` or equivalent) that lists modified files and their `# doc:` targets
- The Stop hook doc-reminder becomes a manual checklist item at session end: "Check which files I modified — do any have `# doc:` annotations that require guide updates?"
- If using VS Code tasks, a post-save task could approximate the PostToolUse hook for file-save events, but not for all tool-use events

**Assessment**: Requires adaptation. The discipline (always check annotations after code changes) translates directly; the automation does not. Cursor adopters accept that this step is manual.

### The `.aiag/config.yaml` resolution chain

Agents in Claude Code resolve project root and config values from `.aiag/config.yaml` via a resolution chain (config file → env var → working directory). Cursor does not invoke agents autonomously, so the resolution chain is not automatically executed.

A Cursor adopter handles this by: including the relevant config values in the Cursor rules or prompt template preamble. For example, a planner prompt template might begin: "Read `.aiag/config.yaml` and use `constitution_path` for the audit step." The config file itself remains valuable as a canonical reference — it just does not get read automatically.

**Assessment**: Requires adaptation. Keep the config file; update prompt templates to explicitly instruct Cursor to read it.

---

## Category 3: What Gets Deferred

### Knowledge graph substrate

The knowledge graph (`scripts/build_knowledge_graph.py`, `docs/knowledge_graph/`) builds a reverse-dependency graph of the codebase. This is a non-trivial infrastructure investment: the script must be configured for the project's source directories, run after each change, and the output must be read by the reviewer agent. For a Cursor adopter who is evaluating the methodology on a new project, this infrastructure does not exist yet and building it is a multi-hour setup task.

**Recommendation**: Defer. Disable in `.aiag/config.yaml` with `substrate_features.knowledge_graph: false`. The reviewer does its job without graph context — it just cannot warn about callers of modified modules automatically. The adopter traces call chains manually until the graph is built.

**Deferral cost**: Reviewer catches fewer reverse-dependency issues on first adoption. This is acceptable risk for a new project.

### DB knowledge graph substrate

The DB knowledge graph (`scripts/build_db_knowledge_graph.py`, `docs/db_knowledge_graph/`) requires a PostgreSQL database with a known schema. For projects without a PostgreSQL database, this feature has no applicability. For projects that do have one, building the graph requires configuring `docs/db_knowledge_graph/build_config.yaml` with correct credentials and running the script.

**Recommendation**: Defer unless the adopter already has a PostgreSQL database with a stable schema. Set `database.db_name` and `database.db_user` in config only when ready to build the graph.

**Deferral cost**: The planner cannot surface table-to-module dependencies when drafting database-affecting phases. Acceptable on first adoption.

### Doc-routing infrastructure

The doc-routing system (`.claude/doc-mapping.yaml`, `# doc:` annotations in source files, `/wrap-change` Step 2) maps source files to their documentation targets. Building this requires: annotating all existing source files with `# doc:` comments, creating a doc-mapping YAML file, and having script guide documents to route updates to. For a new project starting fresh, this infrastructure does not exist yet.

**Recommendation**: Defer. Disable in `.aiag/config.yaml` with `substrate_features.doc_routing: false`. The adopter keeps documentation current manually until the annotation infrastructure is built. The `# doc:` annotation convention is documented in `.aiag/templates/script_guide_template.md` for when the adopter is ready.

**Deferral cost**: Documentation updates after code changes are entirely manual. On a new project with a small codebase, this is manageable.

---

## Harder Than Expected: Push-Back Moments

### The review chain without subagents is a discipline commitment

When reasoning through Category 2, the temptation is to say "the review chain just becomes multiple conversation turns." That is true but it understates what it requires. In Claude Code, the review chain fires automatically — the adopter does not decide whether to run the reviewer, it runs. In Cursor, the adopter decides. The decision point is where discipline erodes: "I'll skip the reviewer this time, the change was small." The methodology's value partially depends on the review chain being non-optional.

A Cursor adopter who is serious about the methodology needs to treat the review chain as a hard commitment, not a guideline. This is harder than it sounds. The automation in Claude Code is not just convenience — it is enforcement.

### Agents carry implicit context that prompt templates do not

The planner agent in Claude Code reads `.aiag/config.yaml`, learns domain context from root `CLAUDE.md`, and applies Pattern Guard checks in a single invocation. When converted to a Cursor prompt template, the adopter must remember to tell Cursor to read those files. If they forget, the planner produces a plan without Pattern Guard enforcement. The agent system's value is partly in making context-loading automatic and non-skippable.

This is recoverable — the prompt template can include explicit instructions to read the config and CLAUDE.md — but it requires the adopter to understand why each file is being read, not just that it should be read.

### Cursor rules are global; roles are situational

The subagent design assumes role isolation: when the reviewer is running, it is only the reviewer. With Cursor rules, any rule marked as "always active" applies to every conversation. If the reviewer's constraints are always active, they may interfere with implementation conversations. If they are not always active, the adopter must remember to invoke them explicitly.

The cleanest Cursor architecture for role isolation is probably separate Cursor workspaces (one per role) or explicit per-conversation role invocation via prompt template. Neither is as clean as Claude Code's subagent mechanism. This is a genuine architectural gap, not a configuration problem.
