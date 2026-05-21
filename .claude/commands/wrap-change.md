---
name: wrap-change
description: Post-implementation wrapper — update docs, stage changes, and prepare a commit message. Run after code-modifying sessions. Use --review to also spawn a reviewer and constitutional auditor before presenting the commit.
argument-hint: "[--review]"
arguments: [mode]
allowed-tools: Read, Grep, Glob, Bash, Edit, Write, Agent
---

The user wants to wrap up a code-modifying session by updating documentation and preparing a commit.

## Step 1: Collect changed files

Determine which files were modified this session:

1. Check for the session temp file at `/tmp/claude_session_$PPID.txt` (where `$PPID` is the parent process ID of the current shell — use `os.getppid()` via Python or `$PPID` in Bash):

```bash
SESSION_FILE="/tmp/claude_session_${PPID}.txt"
if [ -f "$SESSION_FILE" ]; then
    CHANGED_FILES=$(cat "$SESSION_FILE")
else
    # Fallback: use git diff for files modified but not yet staged
    CHANGED_FILES=$(git -C $PROJECT_ROOT diff --name-only; git -C $PROJECT_ROOT diff --name-only --cached)
fi
```

Collect the unique list of changed files. If no files are found, tell the user there is nothing to wrap and stop.

## Step 2: Parse `# doc:` annotations

Read `substrate_features.doc_routing` from `.aiag/config.yaml`. If the key is absent, default to `true`.

**If `doc_routing` is false**: skip this entire step. No annotation scanning or fallback lookup is needed. Proceed directly to Step 3 with an empty script guide file set.

**If `doc_routing` is true**: for each changed code file (`.py`, `.js`, `.html`), scan its content for `# doc:` annotation lines. These are comment lines in the format:

```python
# doc: docs/script_guide/utility_scripts.md
```

or in JS/HTML:

```javascript
// doc: docs/script_guide/content_generation.md
```

The annotation names the target script_guide file that documents this module. Collect the set of unique script_guide files referenced across all changed files.

If a changed file has no `# doc:` annotation, fall back to `.aiag/doc-mapping-fallback.json`: look up the file's path in the `module_path` field and read the corresponding `doc_file` field to determine the target guide.

## Step 3: Determine documentation targets

Build the full documentation update list:

1. **Script guide files**: the set collected in Step 2 (from `# doc:` annotations or doc-mapping-fallback.json lookup)
2. **CLAUDE.md files**: for each changed file, resolve the matching CLAUDE.md via `.claude/doc-mapping.yaml` path-prefix rules — find the longest matching `path_prefix` and use its `updates` list entries that end in `CLAUDE.md`
3. **CHANGELOG.md**: always included (`docs/CHANGELOG.md`)

## Step 4: Run the doc-updater pass

For each documentation target identified in Step 3, perform the following update:

**For script guide files** (`docs/script_guide/*.md`):
- Read the current script guide file
- For each changed code file that maps to this guide, check whether the function signatures, module description, or entry points described in the guide are still accurate relative to the current code
- If any descriptions are stale (function renamed, parameter changed, new function added), update the relevant section
- If the code file is new (not yet described in the guide), append a new section following the guide's existing format

**For CLAUDE.md files**:
- Read the CLAUDE.md and the changed code file
- Check whether the CLAUDE.md's module description, entry points, or configuration keys accurately reflect the current code state
- If stale, update the relevant lines only (surgical edit — touch only what changed)

**For CHANGELOG.md** (`docs/CHANGELOG.md`):
- Append a new entry under the current date section (or create a new date section if today's date is not present)
- Summarize the changes: what was modified, why, and which files were touched

## Step 5: Rebuild the knowledge graph and stage script guide files

This step has two independently-gated operations. Read `.aiag/config.yaml` for both feature flags.

### 5a: Knowledge graph rebuild

Read `substrate_features.knowledge_graph` from `.aiag/config.yaml`. If the key is absent, default to `true`.

**If `knowledge_graph` is false**: skip this operation and print: "Knowledge graph rebuild skipped — substrate feature disabled."

**If `knowledge_graph` is true**:

```bash
# Read graph_build_venv from .aiag/config.yaml (key: graph_build_venv). If the key is absent or
# the config file does not exist, skip the graph rebuild and print a note.
GRAPH_VENV=$(python3 -c "
import yaml, sys
try:
    with open('.aiag/config.yaml') as f:
        cfg = yaml.safe_load(f)
    v = cfg.get('graph_build_venv')
    print(v if v else '', end='')
except Exception:
    print('', end='')
" 2>/dev/null)
if [ -n "$GRAPH_VENV" ]; then
    source $PROJECT_ROOT/$GRAPH_VENV/bin/activate
    python3 $PROJECT_ROOT/scripts/build_knowledge_graph.py
    python3 $PROJECT_ROOT/scripts/build_db_knowledge_graph.py
    deactivate
else
    echo "Note: graph_build_venv not configured in .aiag/config.yaml — skipping knowledge graph rebuild."
fi
```

Do NOT stage `docs/db_knowledge_graph/db_graph.json` or `docs/db_knowledge_graph/schema.json` — they are gitignored local artifacts.

Do NOT stage `docs/knowledge_graph/graph.json` — it is gitignored and must remain a local artifact.

### 5b: Script guide staging

Read `substrate_features.doc_routing` from `.aiag/config.yaml`. If the key is absent, default to `true`.

**If `doc_routing` is false**: skip this operation and print: "Script guide staging skipped — doc_routing feature disabled."

**If `doc_routing` is true**: read `script_guide_dir` from `.aiag/config.yaml` (default: `docs/script_guide`) and stage all script guide files so that any injection changes made in Step 5a are captured, regardless of which files the doc-updater touched:

```bash
SCRIPT_GUIDE_DIR=$(python3 -c "
import yaml
try:
    with open('.aiag/config.yaml') as f:
        cfg = yaml.safe_load(f)
    print(cfg.get('script_guide_dir', 'docs/script_guide'), end='')
except Exception:
    print('docs/script_guide', end='')
" 2>/dev/null)
git -C $PROJECT_ROOT add ${SCRIPT_GUIDE_DIR}/*.md
```

## Step 6: Stage all changes

```bash
git -C $PROJECT_ROOT add <all modified files including doc targets>
```

Stage both the code files and the documentation files updated in Step 4.

## Step 7: Conditional — `--review` mode

Check whether `$mode` equals `--review` (the first argument passed to this skill). If so:

1. **Spawn a reviewer subagent** using the Agent tool:
   - subagent_type: `reviewer`
   - Pass context: the list of changed code files and a brief description of what was modified
   - The reviewer checks code quality on 4 axes: simplicity, surgical precision, verify coverage, Article I compliance

2. **After the reviewer completes**, read its Result block. If `blocking: true`, report the blocking findings to the user and stop — do not present the commit message until the issues are fixed.

3. If reviewer passes, **spawn a constitutional-auditor subagent** using the Agent tool:
   - subagent_type: `constitutional-auditor`
   - Pass context: the list of changed files
   - The auditor checks the changes against the configured project constitution

4. After the auditor completes, read its Result block. If `blocking: true`, report the findings and stop.

## Step 8: Present commit message for human approval

Draft a commit message following the repository's established style (read recent commits with `git -C $PROJECT_ROOT log --oneline -10` for style reference). The message should:
- Have a concise subject line (type(scope): description)
- Body summarizing what changed and why
- End with `Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>`

**Present the message to the user and ask for approval. Do NOT run `git commit` automatically.** Tell the user:

> "Review with `git diff --cached`. If everything looks good, run `git commit` with the message above."

## Step 9: Clean up temp file

Always delete the session temp file at the end, regardless of whether code files were found or whether the flow succeeded:

```bash
rm -f "/tmp/claude_session_${PPID}.txt"
```

## Important

- This skill updates docs and stages changes but does NOT commit. The human commits after reviewing the staged diff.
- The `# doc:` annotation is the primary routing mechanism. If a changed file has no annotation and is not in `.aiag/doc-mapping-fallback.json`, skip it (log a warning) rather than guessing the wrong doc file.
- Always use `$PPID` (parent process ID of the current shell), not `$$` (the current process), for the session temp file path — this matches the path written by the PostToolUse hook.
- In `--review` mode, the reviewer and auditor run serially with short-circuit. Never run the auditor if the reviewer blocks.
