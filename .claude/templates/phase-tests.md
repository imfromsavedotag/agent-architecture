# Phase {N} Tests — {Plan Name}

## Test Scope

{How test scope was determined from the phase checklist — which modules were touched, which test files apply.}

## Command

```bash
{exact command used to run the tests, including venv activation if needed}
```

## Summary

| Metric | Value |
|--------|-------|
| Tests run | {count} |
| Passed | {count} |
| Failed | {count} |
| Skipped | {count} |
| No tests exist | {list of touched modules with no test coverage, or "N/A"} |

## Failure Details

{For each failure: test name, file:line, error message, and brief analysis. If all passed, state "All tests passed."}

---

## Result
status: pass | fail | warn
blocking: {true if any test failed, false otherwise}
summary: {passed}/{total} passed{, N modules lack test coverage if applicable}
