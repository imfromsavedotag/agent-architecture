---
name: onboard
description: Configure the methodology for a new project via an interactive interview
allowed-tools: Read, Write, Bash, Glob
---

The user wants to configure the methodology for their project. This skill conducts a direct interview in the conversation — no subagent spawn. After gathering answers, it writes `.aiag/config.yaml` and optionally initializes `.claude/learnings.md`.

## Instructions

### 1. Overwrite guard

Check whether `.aiag/config.yaml` already exists.

```bash
ls .aiag/config.yaml 2>/dev/null && echo "EXISTS" || echo "NOT_FOUND"
```

If the file exists, stop and ask the user: "`.aiag/config.yaml` already exists. Running the interview will overwrite your current configuration. Do you want to continue? (yes/no)"

- If the user says **no** or anything other than yes/y: thank them and end the skill.
- If the user says **yes** or **y**: proceed with the interview.

If the file does not exist, proceed directly.

### 2. Establish project root

Read the current working directory from the environment. Display it to the user and confirm it is the project root:

"Your project root appears to be `<cwd>`. Is this correct?"

- If no: ask them to specify the correct project root path before continuing.
- If yes: proceed.

### 3. Conduct the interview

Ask the following questions in order, one group at a time. Present each question clearly. For optional keys, include a plain-language consequence statement explaining what the adopter will have to do themselves if they skip it.

---

**Group 1 — Project identity**

Q1: "What is your project's display name? (used in agent reports and identity strings)"
→ Captures `project_name`

Q2: "What file extensions does your project's source code use? List them comma-separated, e.g.: .py, .js, .ts"
→ Captures `language_extensions` as a YAML list

---

**Group 2 — Principles document**

Q3: "Do you have a principles or constitution document — a file that states your project's non-negotiable rules? If yes, give the path relative to the project root (e.g. `docs/constitution.md`). If no, press Enter to skip."

**Skip consequence**: "If you skip this, the constitutional-auditor subagent will have no document to audit against and the compliance-check step at plan closeout will be a no-op. You can add a constitution later by updating `constitution_path` in `.aiag/config.yaml`."

→ Captures `constitution_path` (null if skipped)

---

**Group 3 — Substrate features**

Q4: "Do you want to enable the **knowledge graph** feature? This builds a reverse-dependency graph of your codebase so the methodology can warn you which modules depend on a file before you change it.

Without it, tracing which parts of your code depend on a module you are about to modify is manual work — you would need to search the codebase yourself before each change.

Answer yes or no."

**If yes**: "What venv path should the graph build script use? (relative to project root, e.g. `backend/venv` or `venv`)"
→ Captures `substrate_features.knowledge_graph: true` and `graph_build_venv`

**If no**: captures `substrate_features.knowledge_graph: false`; `graph_build_venv` is set to null.

---

Q5: "Do you want to enable the **doc-routing** feature? This maps source files to their documentation targets so the methodology knows which guide pages to update when you change code.

Without it, keeping documentation in sync with code changes is entirely manual work — you would need to locate and update the relevant docs yourself after every change.

Answer yes or no."

→ Captures `substrate_features.doc_routing: true/false`

---

**Group 4 — Documentation directories**

Q6: "Do you maintain a script or module guide directory — a folder of Markdown files documenting individual modules? If yes, give the path relative to the project root (e.g. `docs/script_guide`). If no, press Enter to skip.

Without this, the reviewer and planner cannot enforce documentation currency checks on module-level docs — those checks will be skipped."

→ Captures `script_guide_dir` (null if skipped)

Q7: "Do you maintain a subsystem audit doc directory — a folder of Markdown files that describe subsystem inventories and entry points? If yes, give the path (e.g. `docs/audit`). If no, press Enter to skip.

Without this, the reviewer cannot check for stale counts in subsystem docs — those checks will be skipped."

→ Captures `audit_doc_dir` (null if skipped)

Q8: "What section name should the phase-bookkeeper use when appending entries to your CHANGELOG? (e.g. `Workflow Automation`, `Unreleased`, `Changes`). Press Enter to skip and let the bookkeeper choose the most relevant section automatically."

→ Captures `default_changelog_section` (null if skipped)

---

**Group 5 — Python and environment**

Q9: "Does your project have a Python environments documentation file — a doc that explains which venvs to use and how to load secrets? If yes, give the path (e.g. `docs/python_environments.md`). If no, press Enter to skip.

Without this, the planner and test-gate will skip the mandatory-reading constraint for Python environments — which means agents may attempt to run Python code in the wrong environment."

→ Captures `python_environments_doc` (null if skipped)

---

**Group 6 — Doc-routing config files**

These questions only apply if doc-routing is enabled (Q5 = yes). If doc-routing is disabled, set both to null and skip to Group 7.

Q10: "Where is your doc-mapping YAML config? This file maps path prefixes to documentation targets and is used by the doc-updater. (e.g. `.claude/doc-mapping.yaml`). Press Enter to use the default: `.claude/doc-mapping.yaml`"

→ Captures `doc_mapping_path` (default: `.claude/doc-mapping.yaml`)

Q11: "Where is your doc-mapping fallback JSON file? This is used when a changed file has no `# doc:` annotation. (e.g. `.aiag/doc-mapping-fallback.json`). Press Enter to use the default: `.aiag/doc-mapping-fallback.json`"

→ Captures `doc_mapping_fallback` (default: `.aiag/doc-mapping-fallback.json`)

---

**Group 7 — Memory**

Q12: "Does your project use a non-standard Claude Code memory directory? Press Enter to use the platform convention (auto-derived from the project root path).

You almost certainly want to press Enter here unless your Claude Code installation has a custom memory layout."

→ Captures `memory_path` (null if Enter/skipped)

---

**Group 8 — Database**

Q13: "Does your project use a PostgreSQL database? If yes, what is the database name and user? (These are used only by the optional DB knowledge graph builder.)

Enter in the format: `db_name, db_user` — for example: `myapp_db, myapp_user`. Press Enter to skip."

→ Captures `database.db_name` and `database.db_user` (both null if skipped; entire `database:` block omitted if skipped)

---

**Group 9 — Confirmation**

Display a summary of all answers and ask: "Does this look correct? (yes/no)"

- If no: ask which answer(s) to change, re-elicit those, and show the summary again.
- If yes: proceed to write.

### 4. Write `.aiag/config.yaml`

Create or overwrite `.aiag/config.yaml` with the answers collected. Use the exact structure below, substituting collected values. Omit the `database:` block entirely if the user skipped Q13.

```yaml
# Methodology configuration — generated by /onboard
# Edit this file directly to change any setting.
# See .aiag/config.yaml.example for full documentation of every key.

# Path to the principles/constitution document (relative to project root).
# The constitutional-auditor reads this at plan closeout.
constitution_path: <constitution_path or null>

# Substrate feature flags
substrate_features:
  knowledge_graph: <true or false>
  doc_routing: <true or false>

# Display name for this project (used in agent identity strings and reports)
project_name: "<project_name>"

# Venv path used by wrap-change Step 5a for graph rebuild scripts.
# Set to null if knowledge_graph is false.
graph_build_venv: <graph_build_venv or null>

# Memory path — set to null to use the platform convention (recommended).
memory_path: <memory_path or null>

# Path to the Python environments documentation (relative to project root).
# Set to null if this project has no Python code or no environment doc.
python_environments_doc: <python_environments_doc or null>

# Directory containing script guide documentation files (relative to project root).
# Set to null to disable script guide doc maintenance constraints.
script_guide_dir: <script_guide_dir or null>

# Directory containing subsystem audit documentation files (relative to project root).
# Set to null to disable audit doc maintenance constraints.
audit_doc_dir: <audit_doc_dir or null>

# Default CHANGELOG section name for phase-bookkeeper.
# Set to null to let the bookkeeper choose the most relevant section automatically.
default_changelog_section: <default_changelog_section or null>

# Path to the YAML doc-mapping config used by /wrap-change Step 2 (doc-updater).
# Set to null if doc_routing is false.
doc_mapping_path: <doc_mapping_path or null>

# Fallback path for the doc-routing module-to-guide mapping file.
# Set to null if doc_routing is false.
doc_mapping_fallback: <doc_mapping_fallback or null>

# File extensions the Stop hook uses to detect code changes.
language_extensions: [<language_extensions>]

# Database connection parameters for the DB knowledge graph builder.
# Omit this block if your project has no database.
database:
  db_name: <db_name>
  db_user: <db_user>
```

### 5. Initialize learnings file (conditional)

Check whether `.claude/learnings.md` already exists.

- If it **does not exist**: check whether `.claude/learnings-header.md` exists.
  - If `.claude/learnings-header.md` exists: copy its contents to `.claude/learnings.md`.
  - If it does not exist: write a minimal learnings header to `.claude/learnings.md`:
    ```markdown
    # Learnings

    Persistent cross-plan learning file. The planner reads this before drafting any new plan.

    processed-plans:

    ---
    ```
- If `.claude/learnings.md` already exists: do not modify it. Note this to the user.

### 6. Report what was configured

Print a brief confirmation:

```
Onboarding complete.

Configuration written to: .aiag/config.yaml
Learnings file: <created at .claude/learnings.md | already existed — not modified>

Enabled features: <list knowledge_graph / doc_routing as enabled/disabled>

Next steps:
- Review .aiag/config.yaml and adjust any values
- Use /plan <slug> to start your first implementation plan
- Use /kickoff-phase <slug> 1 before implementing each phase
```
