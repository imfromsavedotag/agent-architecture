# {Plan name}

<!--
This is the canonical structure for AIAg implementation plans. Do not abbreviate
the checkpoint blocks within phases — they are inline by design, so an agent
working on one subtask has the continuation rule in immediate context rather
than scrolled out of view.
-->

## Primer

- **Status**: {draft | in-progress | complete | abandoned}
- **Executor**: {model identifier} (working budget: {B} tokens)
- **Phases total**: {N}
- **Phases complete**: {M}
- **Next phase**: {phase number and short title, or "complete"}
- **Reading for next phase**: see Phase {next} reading list

---

## Phase 1: {phase title}

### Objective

{One or two sentences describing what this phase produces. State the deliverable, not the activity.}

### Context budget estimate

```
- Reading list:    {X} tokens
- Code generation: {Y} tokens
- Buffer (25%):    {Z} tokens
- Total:           {T} tokens
- Working budget:  {B} tokens
- Headroom:        {B - T} tokens
```

{If this phase was split from a larger phase, note the split rationale here in one sentence.}

### Reading list

- `{path/to/file}` `[full-read]` — {why this file is needed}
- `{path/to/file}` `[search/grep]` — {what to look for}
- ...

### Subtasks

#### Subtask A: {short title}

- [ ] {checklist item}
  -> verify: {observable passing condition}
- [ ] {checklist item}
  -> verify: {observable passing condition}

##### Checkpoint after Subtask A

- [ ] Update STATE.md: mark subtask complete; append to "Files modified", "Decisions made", "Open questions"
- [ ] Report progress signals (one line each):
  - subtasks complete: {X} of {Y}
  - unplanned events since last checkpoint: "none" OR a description covering any of:
    - retry storms (any single step retried more than 2 times)
    - scope shifts (work needed that isn't in the plan)
    - file reads materially larger than the plan estimated
- [ ] Apply continuation rule:
  - **Continue** if unplanned events = "none"
  - **Stop** if any unplanned event occurred — write a Resume note to STATE.md before ending the session
- [ ] If the phase is now complete, mark the phase complete in STATE.md and update the primer block above

#### Subtask B: {short title}

- [ ] {checklist item}
  -> verify: {observable passing condition}
- ...

##### Checkpoint after Subtask B

- [ ] Update STATE.md: mark subtask complete; append to "Files modified", "Decisions made", "Open questions"
- [ ] Report progress signals (one line each):
  - subtasks complete: {X} of {Y}
  - unplanned events since last checkpoint: "none" OR a description covering any of:
    - retry storms (any single step retried more than 2 times)
    - scope shifts (work needed that isn't in the plan)
    - file reads materially larger than the plan estimated
- [ ] Apply continuation rule:
  - **Continue** if unplanned events = "none"
  - **Stop** if any unplanned event occurred — write a Resume note to STATE.md before ending the session
- [ ] If the phase is now complete, mark the phase complete in STATE.md and update the primer block above

### Acceptance criteria

- {observable criterion 1}
- {observable criterion 2}
- ...

---

## Phase 2: {phase title}

### Objective

{...}

### Context budget estimate

```
- Reading list:    {X} tokens
- Code generation: {Y} tokens
- Buffer (25%):    {Z} tokens
- Total:           {T} tokens
- Working budget:  {B} tokens
- Headroom:        {B - T} tokens
```

### Reading list

- ...

### Subtasks

#### Subtask A: {short title}

- [ ] ...
  -> verify: ...

##### Checkpoint after Subtask A

(same checkpoint block as above — repeat verbatim at every subtask boundary)

### Acceptance criteria

- ...

---

{Continue for all phases. Each phase is self-contained: its reading list must include everything needed to implement it.}
