# State — {plan name}

<!--
Updated by the executing agent at every subtask checkpoint and at session end.
Read at session resume to recover phase state without re-loading full plan history.

Discipline: STATE.md captures committed state at boundaries, not in-flight thinking.
Keep it lean — it gets re-read every resume, and verbose state defeats its purpose.
-->

---

## Current status

- **Active phase**: {N — phase title, or "complete" if all phases done}
- **Phase status**: {not started | in progress | complete}
- **Last session ended**: {cleanly at subtask {X} | exhausted mid-subtask {X} | resumed and continuing}
- **Last updated**: {ISO timestamp}

---

## Phase {N} progress

- Subtask A — {title}: {not started | in progress | complete (verified)}
- Subtask B — {title}: ...
- Subtask C — {title}: ...

### Files modified this phase

- `{path}` — {one-line nature of change}
- ...

### Decisions made

- {decision in one line} — {one-line rationale}
- ...

### Open questions

- {question}
- ...

### Resume note

{Populated when a session ends mid-phase. Empty if the last session ended at a clean phase or subtask boundary.

When populated, describe what the next session needs to know to pick up cleanly:
- Where work paused and why
- What was about to happen next
- Any partial state that needs cleanup or verification
- Anything that wouldn't be obvious from re-reading PLAN.md alone}

---

## Completed phases

### Phase 1 — {title}

- Completed: {date}
- Subtasks: {comma-separated list}
- Files modified: {short list, or "see commit {sha} for full diff"}
- Notable decisions carried forward: {short list, or "none"}

### Phase 2 — {title}

(same shape, added when phase completes)

---
