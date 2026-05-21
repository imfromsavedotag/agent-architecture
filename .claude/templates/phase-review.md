# Phase {N} Review — {Plan Name}

## Simplicity

{Does the implementation use the minimum code that solves the stated problem? Flag unrequested abstractions, speculative configurability, or premature generalization.}

- {Finding or "No issues."}

## Surgical Precision

{Does every changed line trace directly to the phase's checklist items? Flag adjacent refactoring, style changes, or improvements to code the phase did not need to touch.}

- {Finding or "No issues."}

## Verify Coverage

{Does each checklist item carry a verify criterion, and was it demonstrated to pass?}

| Checklist Item | Verify Criterion | Demonstrated |
|----------------|-----------------|--------------|
| {item} | {criterion} | {yes/no + evidence} |

## Documentation Accuracy

{For any documentation files created or modified by this phase, do all file paths exist? Do all technical references (model names, table/column names, function names) match current code? Any stale cross-references?}

- {Finding or "No issues." or "No documentation files modified."}

## Findings

### Blocking

- {file:line — description of issue. Empty section if none.}

### Non-blocking

- {file:line — description of issue. Empty section if none.}

### Suggestions

- {file:line — description of suggestion. Empty section if none.}

---

## Result
status: pass | fail | warn
blocking: {true if any blocking findings, false otherwise}
summary: {blocking count} blocking, {non-blocking count} non-blocking, {suggestion count} suggestions
